# C++ 规则查询索引

本文件是 **导航与特例登记表**，不是新的 Source of Truth。规则优先级、适用范围与加载流程由 [SKILL.md](SKILL.md) 管理；实际布局以 [formatting-and-layout.md](formatting-and-layout.md)、注释以 [comments-and-documentation.md](comments-and-documentation.md) 为准。示例参见 [排版回归样例](tests/formatting-cases.md)。

## 最小查询流程

1. 识别本次改动涉及的**完整结构单元**，同时识别其中嵌套的结构（例如函数内的 `switch`、回调 Lambda、初始化列表）。
2. 读取格式规范的 [Baseline](formatting-and-layout.md#baseline)、[Encoding and line endings](formatting-and-layout.md#encoding-and-line-endings)、[Indentation](formatting-and-layout.md#indentation)、[Braces](formatting-and-layout.md#braces)、[Blank lines](formatting-and-layout.md#blank-lines)、[Horizontal whitespace](formatting-and-layout.md#horizontal-whitespace)。
3. 按下表读取**所有命中的**专题及其例外；同一结构命中多行时合并应用，不能只看表格中的摘要。
4. 检查项目已明确授权的机械排版覆盖；检查 [注释规范](comments-and-documentation.md) 中与职责、接口契约、生命周期、回调或错误处理相关的章节。
5. 检查最终 Diff 和适用的 [Golden Cases](tests/formatting-cases.md)。不能判断特例是否满足时，回读整份相关权威文件，而不是猜测。

## 按代码结构查找

| 代码结构 / 需求 | 权威章节 | 相关特例 / 样例 |
|---|---|---|
| 函数、控制流、类、namespace 的花括号 | [Braces](formatting-and-layout.md#braces)、[Blank lines](formatting-and-layout.md#blank-lines) | FMT-NS-001、FMT-SWITCH-001 |
| 单行 / 多行函数签名 | [Function declarations and definitions](formatting-and-layout.md#function-declarations-and-definitions) | FMT-WRAP-001 |
| 函数调用、复杂参数 | [Expressions and function calls](formatting-and-layout.md#expressions-and-function-calls) | FMT-WRAP-001 |
| 返回值、花括号返回 | [Return statements](formatting-and-layout.md#return-statements) | 单行优先；大括号返回同时检查 FMT-INIT-001 |
| 模板声明、多重继承 | [Templates and inheritance](formatting-and-layout.md#templates-and-inheritance) | 续行对齐与构造函数冒号/逗号写法不同于普通参数 |
| 构造函数、成员初始化、聚合 / 容器初始化 | [Constructors and initializer lists](formatting-and-layout.md#constructors-and-initializer-lists)、[Member initialization](formatting-and-layout.md#member-initialization) | FMT-INIT-001 |
| `switch`、单行 `case`、fallthrough | [`switch` and `case`](formatting-and-layout.md#switch-and-case) | FMT-SWITCH-001 |
| namespace 与匿名 namespace | [Namespaces](formatting-and-layout.md#namespaces-forward-declarations-and-using) | FMT-NS-001 |
| 逻辑条件、三目表达式、续行对齐 | [Conditions and boolean expressions](formatting-and-layout.md#conditions-and-boolean-expressions) | FMT-CONT-001 |
| Lambda 与回调注册 | [Lambdas and callbacks](formatting-and-layout.md#lambdas-and-callbacks)、[Expressions and function calls](formatting-and-layout.md#expressions-and-function-calls) | FMT-LAMBDA-001 |
| 预处理指令与多行宏 | [Macros, constants, enums, and static symbols](formatting-and-layout.md#macros-constants-enums-and-static-symbols) | FMT-MACRO-001 |
| include 分组、头文件类成员排序 | [Include layout](formatting-and-layout.md#include-layout)、[Header declaration grouping](formatting-and-layout.md#header-declaration-grouping)、[Source definition grouping](formatting-and-layout.md#source-definition-grouping) | 不允许扩张到无关修改 |
| 命名、循环、转换、所有权 | [Basic naming](formatting-and-layout.md#basic-naming)、[Loops](formatting-and-layout.md#loops-and-containers)、[Auto / casts](formatting-and-layout.md#auto-and-casts)、[Ownership](formatting-and-layout.md#ownership-and-parameter-passing) | 对新增和实际修改的结构生效 |
| 函数体局部流程、错误与日志 | [Function-body readability](formatting-and-layout.md#function-body-readability)、[Error handling](formatting-and-layout.md#error-handling-return-values-and-logs) | 不以排版为借口改变失败路径 |
| 文件结构、header guard | [Header guards / file organization](formatting-and-layout.md#header-guards-file-names-and-file-organization) | 不重组未修改文件 |
| 注释、公共 API、异步生命周期 | [Comments and documentation](comments-and-documentation.md) 的对应章节 | 修改后检查注释是否失真 |

## 特例登记（提示，不替代权威条文）

| ID | 默认规则 | 特例允许的条件 | 不允许的推断 |
|---|---|---|---|
| **FMT-NS-001** | 不在花括号内侧机械插入空行 | **非空**命名或匿名 namespace 的内侧前后各**一行**空行 | 类、函数、循环不能套用 |
| **FMT-SWITCH-001** | Allman 块、每个 case 独立作用域 | `case` 只含一个简单动作加无条件 `break` / `return`，或只含后者时可采用单行作用域 | 局部变量、注释、宏、嵌套流程、fallthrough 不能用单行 |
| **FMT-INIT-001** | 普通非空单行花括号内部各一空格 | 同质连续对象表格可按列对齐，行末 `}` 前允许最小对齐填充 | 不得跨不同表格 / 分组追加空白 |
| **FMT-WRAP-001** | 可读时保留单行签名和调用 | 真正复杂或明显难读时，才按首参数垂直对齐续行 | 参数数量、120 列软阈值、附近旧代码都不能单独强制换行 |
| **FMT-CONT-001** | 逻辑运算符续行放行首 | 赋值以 `=` 为锚点；条件以 `(` 后首操作数为锚点；直接返回以首操作数为锚点 | 不能在无赋值的 `if` 中假设存在 `=` |
| **FMT-LAMBDA-001** | Lambda 采用 Allman；不得单行 | 简单转发 Lambda 可直接留在调用现场，多语句内联 Lambda 按职责和生命周期判断 | “在调用现场”不等于允许单行 Lambda |
| **FMT-MACRO-001** | C++ 语句按层级缩进 | 预处理指令的 `#` 必须位于物理第 1 列 | 不要把内部 C++ 语句也移到第 1 列 |

## 冲突与缺失处理

- **权威优先**：当前明确要求 → 明确授权的项目规则 → 本 Skill 的规范文件；本索引和 Golden Cases 不覆盖它们。
- **特例必须命中前置条件**：未命中则使用默认规则，不能从某个例子推广新排版风格。
- **未覆盖的代码形态**：不凭空定义例外；先回读完整相关章节，并以最小改动保留行为，必要时报告无法确定的点。
- **规则更新**：先改权威条文，再同步本表和相关 Golden Cases；避免只修改示例而造成新冲突。
