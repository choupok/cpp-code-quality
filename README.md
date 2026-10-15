# C++ Code Quality

一个面向 Codex 的 C++17 代码质量 Skill，用统一规则约束代码排版、命名、注释、局部可读性和遗留代码修改边界。

## 为什么需要这个 Skill

不同项目、历史代码和开发工具经常混用不同的缩进、花括号、命名和注释习惯。Codex 如果只模仿附近代码，容易继续放大已有的不一致，或者为了“统一风格”制造大范围无关 diff。

`cpp-code-quality` 提供一套稳定基线：只整理当前任务涉及的完整结构单元，在保证代码可读的同时控制修改范围。项目有明确且经过授权的 C++ 规范时，项目规则仍可按优先级覆盖其中的机械格式要求。

本 Skill 在创建、修改、重构、格式化或聚焦审查 C++ 代码时启用，也适用于用户明确要求的 C++ 排版、分组、命名和注释只读审查。仅进行调用链追踪、影响面分析、仓库事实核对等顶层只读调查时，不应仅因读取了 C++ 文件而触发本 Skill。

## 它规范什么

- Tab 缩进、Allman 花括号、空格、空行、换行和列对齐。
- 函数声明、调用、返回、构造函数、初始化列表、模板和继承布局。
- 条件、循环、`switch`、预处理指令和空语句。
- 类、头文件、源文件和职责分组。
- 类型、函数、变量、成员、常量、枚举和宏的命名。
- 中文注释、接口语义、所有权、生命周期、错误路径和 TODO/FIXME。
- 修改完整结构单元，同时避免无关的全文件格式化。

完整规则及正反例见[中文规则与示例说明](docs/cpp-code-quality-guide.zh-CN.md)。

## 它不负责什么

本 Skill 不替代架构设计、模块拆分、API 归属、构建系统、编译验证、静态分析、运行态验证或性能分析，也不会仅为追求全局一致而批量清理遗留代码。

它可以指出直接影响当前代码可读性、所有权、回调生命周期和错误路径的基础问题，但不会自行决定是否引入 Manager、Service、Facade、Builder 等架构角色。

## 支持的 Codex 客户端

第一版使用标准 Skill 目录结构，面向以下客户端：

- ChatGPT 桌面端中的 Codex；
- Codex CLI；
- Codex IDE 扩展。

客户端需要支持从用户级或仓库级 `.agents/skills` 目录发现 Skill。安装后如未立即出现，请重新加载客户端或重新打开任务。

## 安装

### 用户级安装（推荐）

用户级安装一次后，可在本机多个项目中使用。

Windows PowerShell：

```powershell
git clone https://github.com/choupok/cpp-code-quality.git "$HOME\.agents\skills\cpp-code-quality"
```

macOS / Linux：

```bash
git clone https://github.com/choupok/cpp-code-quality.git "$HOME/.agents/skills/cpp-code-quality"
```

### 项目级安装

需要团队共享并锁定版本时，可将 Skill 作为 Git submodule 放入项目：

```bash
git submodule add https://github.com/choupok/cpp-code-quality.git .agents/skills/cpp-code-quality
git -C .agents/skills/cpp-code-quality checkout v0.1.0
```

提交项目仓库中的 `.gitmodules` 和 submodule 指针后，团队成员可使用：

```bash
git clone --recurse-submodules <project-repository-url>
```

### 下载 ZIP

不使用 Git 时，可从 GitHub 下载 ZIP，解压后将完整目录复制到：

```text
$HOME/.agents/skills/cpp-code-quality
```

无论使用哪种方式，`SKILL.md` 都必须直接位于 `cpp-code-quality` 目录根部，不能多套一层解压目录。

## 使用

当任务涉及创建、修改、重构、格式化或审查 C++ 头文件与源文件时，Codex 可以根据 `SKILL.md` 的描述自动选择本 Skill。

关键任务建议显式点名：

```text
请使用 $cpp-code-quality 修改这段 C++ 代码。
```

更多示例：

```text
请整理这个类的声明顺序和命名。

请使用 $cpp-code-quality 格式化当前修改涉及的完整函数。

请按该 Skill 审查 main...HEAD 中的 C++ 变更，但不要修改文件。
```

自动触发取决于客户端能否发现 Skill 以及当前任务的匹配程度。需要确保采用规则时，应显式使用 `$cpp-code-quality`。

## 规则来源与优先级

Agent 的规范来源与查询入口区分如下：

- `SKILL.md` — 规则加载、优先级、适用范围与验收约束；
- `RULE_INDEX.md` — 按代码结构定位规范和特例的导航表，**不是**另一份规则源；
- `formatting-and-layout.md` — 格式与布局的权威规则；
- `comments-and-documentation.md` — 注释与文档的权威规则；
- `tests/formatting-cases.md` — 正反例回归样本，不覆盖权威规则。

按 `SKILL.md` 加载所有相关规则，不要求每次无条件全文预读。遇到歧义回到权威规则源，不根据旧代码或速查摘要自行补造例外。

中文规则文档面向人阅读，是运行时规则的配套说明，不是独立规则源。两者不一致时，以运行时文件为准；规则变更应在同一次修改中同步更新中文说明。

## 仓库结构

```text
cpp-code-quality/
├── README.md
├── SKILL.md
├── RULE_INDEX.md
├── formatting-and-layout.md
├── comments-and-documentation.md
├── tests/
│   └── formatting-cases.md
├── agents/
│   └── openai.yaml
├── docs/
│   └── cpp-code-quality-guide.zh-CN.md
├── .gitattributes
└── LICENSE
```

## 更新

用户级 Git 安装可执行：

```powershell
git -C "$HOME\.agents\skills\cpp-code-quality" pull --ff-only
```

项目级 submodule 由项目仓库统一更新并提交新的版本指针。规则发生变化后，建议重新加载 Codex 客户端。

## License

本项目采用 [MIT License](LICENSE)。
