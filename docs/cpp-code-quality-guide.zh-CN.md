# C++ Code Quality 中文规则与示例说明

本文档面向使用和维护 C++17 代码的开发者，说明 `cpp-code-quality` Skill 实际采用的排版、命名、注释和基础代码质量规则。

Agent 执行任务时，以仓库根目录的 `SKILL.md`、`formatting-and-layout.md` 和 `comments-and-documentation.md` 为运行时规则来源。可先通过 `RULE_INDEX.md` 按代码结构查询对应章节及特例，并参考 `tests/formatting-cases.md` 的正反例；这两个辅助文件不构成独立规范。本文档是便于人阅读的配套说明；若内容不一致，以运行时文件为准，并应在后续修改中同步修正。

## 目录

1. [规范定位、适用范围与修改边界](#1-规范定位适用范围与修改边界)
2. [规则优先级与项目例外](#2-规则优先级与项目例外)
3. [编码、换行、行宽与 Tab](#3-编码换行行宽与-tab)
4. [花括号、空行与水平空格](#4-花括号空行与水平空格)
5. [函数声明、调用与返回](#5-函数声明调用与返回)
6. [构造函数、模板、继承与初始化列表](#6-构造函数模板继承与初始化列表)
7. [条件、循环、switch 与预处理指令](#7-条件循环switch-与预处理指令)
8. [类、头文件、源文件与职责分组](#8-类头文件源文件与职责分组)
9. [命名规范](#9-命名规范)
10. [注释规范](#10-注释规范)
11. [可读性与基础代码质量](#11-可读性与基础代码质量)
12. [参考资料与差异说明](#参考资料与差异说明)

## 1. 规范定位、适用范围与修改边界

本规范用于创建、修改、重构、格式化或聚焦审查以下 C++ 文件：

- `.h`、`.hpp`、`.hh`
- `.cpp`、`.cc`、`.cxx`

用户明确要求只读审查 C++ 排版、分组、命名或注释时也适用本规范。仅进行调用链追踪、影响面分析、仓库事实核对等顶层只读调查，且尚不需要作出 C++ 代码质量判断时，不应仅因读取了 C++ 文件而启用本规范。

纯 C 文件不强制套用 C++ 的类、构造函数、成员或命名规则。第三方、vendored、generated 和构建输出默认不修改；用户明确要求修改时，应保持上游风格并缩小改动。

### 完整结构单元

一次改动的最小整理边界是“当前确实被修改的完整结构单元”，例如完整函数、完整声明、完整构造初始化列表、完整 `switch`、完整枚举或同一职责分组。

修改函数中的一条语句时，应保证整个函数内部不混用两套缩进和花括号风格；但不能因此格式化同文件中无关的其他函数。

```cpp
void MenuController::refresh()
{
	if (!m_initialized)
	{
		return;
	}

	rebuild_items_();
}
```

### 修改边界

允许同步调整与当前改动直接相关的声明、定义、成员和注释，例如：

- 同一接口的头文件声明和源文件定义；
- 新函数所属的职责分组；
- 因行为变化而失真的接口注释；
- 修改一个 `case` 时的完整 `switch`。

不允许仅因为“同文件看起来不统一”就重排、重命名或格式化无关代码。遗留代码是行为和兼容性参考，不是新代码的样式来源。

本规范不决定架构归属、模块拆分、API 合并、构建系统或运行时策略。

## 2. 规则优先级与项目例外

规则按以下顺序生效：

1. 当前请求中用户明确提出的要求；
2. 已明确授权的项目级 C++ 规范；
3. 本 Skill 的排版与布局规则；
4. 本 Skill 的注释与文档规则。

项目中存在 `.clang-format`、格式化脚本或附近代码采用另一种风格，并不自动代表它们可以覆盖本规范。只有用户明确要求使用项目格式，或项目规范明确声明某个工具为权威格式来源时，机械格式规则才可覆盖本规范。

即使项目格式化工具获得授权，它也只能覆盖缩进、花括号、换行、指针绑定等机械规则，不能替代修改范围、职责分组、注释语义、命名和第三方代码边界。

不要为了应用项目规则而自动执行全文件格式化。局部改动仍应控制在当前任务涉及的完整结构单元内。

## 3. 编码、换行、行宽与 Tab

### 编码和换行

本 Skill 仓库中的文本文件使用 UTF-8 无 BOM 和 LF。

新建 C++ 文件默认使用 UTF-8 无 BOM。换行符优先遵循目标仓库的明确约定；项目没有约定时使用 LF。

修改已有 C++ 文件时保留原编码和换行符。遇到 GBK、CP936 或其他遗留编码时，不得在未确认编码的情况下直接写入中文；完整编码转换必须获得明确授权，也不能在局部修改中顺带批量转换换行符。

Visual Studio 工程和工具生成文件是例外，包括：

- `.sln`
- `.vcxproj`
- `.vcxproj.filters`
- `.vcxitems`
- `.props`
- `.targets`

这些文件保留现有文件或 Visual Studio 默认生成的编码、BOM、换行、XML 缩进和布局，不强制转换为 UTF-8 无 BOM。

### 行宽

可见字符以 120 列为软上限。超过 120 列不等于必须拆分；以下内容可以保留：

- URL；
- 无法安全拆分的字符串；
- `#include`；
- 生成内容；
- 项目规则明确要求保留的内容。

如果拆分会产生比原代码更难读的巨大对齐空白，应提取有业务含义的局部变量或改写控制流。

### Tab

Tab 同时优先用于作用域缩进和同质表格中的列对齐。默认按 Tab 宽度 4 展示；项目中经过授权的 `.editorconfig` 或 `.clang-format` 可以覆盖显示宽度。

不得用四个空格模拟一级 Tab。只有 Tab 无法到达目标列时，才使用最少量空格微调；能够用 Tab 完成的列对齐，不应堆叠大量空格。

多列数据表式对齐只用于同一连续语义块中的同质初始化项或紧凑枚举注释，不跨空行、职责分组或预处理分支对齐。函数调用、函数签名、逻辑表达式和三目表达式确需换行时的续行对齐不受这条限制。

## 4. 花括号、空行与水平空格

### Allman 花括号

函数、控制流、类、结构体、枚举、命名空间和 Lambda 都使用 Allman 风格，并且控制流始终保留花括号。例外仅限满足条件的短 `case` 独立作用域可采用单行块，不能类推到 `if`、Lambda 等结构。非空 namespace 的 `{` 后与 `}` 前各留一行空行；普通函数、类、控制流不使用这条 namespace 专属特例。

不推荐：

```cpp
if (is_ready) {
	start();
} else {
	stop();
}
```

推荐：

```cpp
if (is_ready)
{
	start();
}
else
{
	stop();
}
```

`catch` 和 `do...while` 同样独占一行，前一个 `}` 与关键字之间不插入空行。

```cpp
try
{
	load_config();
}
catch (const ConfigError& error)
{
	handle_config_error(error);
}

do
{
	process_next();
}
while (has_pending_item());
```

### 空行

- 函数定义之间保留一个空行。
- 不连续使用两个或更多空行。
- 花括号内部开头和结尾不留空行。
- 职责分组之间保留一个空行。
- 函数内部只在前置检查、数据准备、核心处理、状态提交等语义阶段之间留空行。
- 空行不得包含 Tab 或空格。

### 水平空格

```cpp
if (is_ready && count > 0)
{
	for (int index = 0; index < count; ++index)
	{
		items[index].refresh();
	}
}
```

具体要求：

- `if (`、`for (`、`while (` 等关键字后有一个空格。
- 圆括号内部不加空格。
- 赋值、算术、比较和逻辑二元运算符两侧各一个空格。
- 一元运算符不加空格，例如 `!is_ready`、`++index`。
- 逗号前无空格，逗号后一个空格。
- 分号前无空格；`for` 中分号后一个空格。
- `.`、`->`、`::` 两侧不加空格。
- 指针和引用与类型绑定：`Type*`、`const Type&`。
- 三目运算符 `?`、`:` 两侧各一个空格。

每行只声明一个普通变量，避免混合指针或引用声明。

不推荐：

```cpp
int first = 0, second = 0;
Widget* left, right;
```

推荐：

```cpp
int first = 0;
int second = 0;
Widget* left = nullptr;
Widget* right = nullptr;
```

结构化绑定，以及 `for` 初始化器中紧密耦合的多个控制变量可以例外。

## 5. 函数声明、调用与返回

### 声明和定义

短且清晰的签名优先单行。不能仅因为参数达到三个或四个就机械换行。

需要换行时，第一个参数留在函数名同一行，后续参数与第一个参数对齐，右括号跟在最后一个参数后面；声明和定义使用相同结构。

```cpp
bool build_runtime_menu(const ProductConfig& product_config,
						const RuntimeFactRegistry& runtime_fact_registry,
						const PermissionSession& permission_session);
```

默认参数只写在声明中。

声明修饰符采用稳定顺序：属性放在声明开头；`explicit` 放在构造函数前；成员函数后缀依次为 `const` 或引用限定、`noexcept`、`override`、`final`。

```cpp
[[nodiscard]] virtual SaveResult save() const noexcept = 0;
void refresh() noexcept override final;
explicit OptionController(DataManager& data_manager);
```

重写函数使用 `override`，不重复写无意义的 `virtual`。同时使用时写成 `override final`。`= default`、`= delete`、`= 0` 放在声明末尾。

### 函数调用

调用保持单行优先。确需换行时，第一个参数留在左括号后，后续参数与第一个参数对齐。

```cpp
register_runtime_handler(RuntimeFactId::GpsAvailable,
						 create_fact_handler(runtime_registry, event_bus),
						 RuntimeUpdatePolicy::NotifyOnChange);
```

不要给普通参数普遍添加注释。遇到外部或遗留 API 的裸布尔值、数字含义不清时，优先改用枚举、Options 或具名变量；无法改接口时可使用参数名注释。

```cpp
send_command(command, /*timeout_ms=*/500, /*retry_count=*/2);
```

注释必须使用接口中的准确参数名，不能翻译或另造名称。

Windows 头文件可能定义 `min` 和 `max` 宏。只要目标代码可能包含 Windows 头文件，标准算法调用必须显式写模板参数，避免 `std::min(...)` 和 `std::max(...)` 被宏展开：

```cpp
const int visible_width = std::min<int>(window_width, content_width);
const std::size_t item_count = std::max<std::size_t>(items.size(), minimum_item_count);
```

不要仅依赖 `NOMINMAX` 让裸写调用保持安全；新代码中也不要使用未限定的 `min` 或 `max`。模板参数应匹配期望的公共类型，操作数类型不一致时应显式转换。该规则适用于新增代码和实质修改的完整结构单元，不要求批量改写无关遗留代码。

### 返回

简单返回保持单行，不写多余括号。

```cpp
return resolve_state();
```

简单、无副作用的三目返回可以单行。复杂返回优先使用提前返回或有业务含义的具名变量。

不要为了立即返回而创建没有语义的 `result`：

```cpp
return build_menu(config);
```

返回聚合对象时遵循初始化列表规则：

```cpp
return { node_id, RuntimeState::Ready };
```

## 6. 构造函数、模板、继承与初始化列表

### 构造函数初始化列表

存在构造初始化列表时，每个初始化项独占一行，使用行首逗号，并与成员声明顺序一致。

```cpp
OptionController::OptionController(DataManager& data_manager, OptionFacade& option_facade)
	: m_data_manager(data_manager)
	, m_option_facade(option_facade)
{
}
```

简单默认状态优先在成员声明处初始化。引用、`const` 成员、不可默认构造对象和构造参数建立的依赖使用初始化列表。

### 花括号初始化

非空单行初始化在花括号内侧保留空格，空初始化直接写 `{}`。

```cpp
const Point origin { 0, 0 };
const std::vector<int> values { 1, 2, 3 };
const RuntimeOptions options {};
```

多行初始化采用 Allman 布局，每项一行，最后一项不加尾随逗号。

```cpp
const std::vector<int> values =
{
	1,
	2,
	3
};
```

多个同类型对象组成连续初始化表时，每个对象可以保持一行，并让相同参数位置纵向对齐。优先用 Tab 对齐，Tab 无法精确到列时再补最少空格。

```cpp
const std::vector<MenuItem> items =
{
	{ ItemId::Home,		"home",		true  },
	{ ItemId::Settings,	"settings",	false },
	{ ItemId::About,		"about",		true  }
};
```

只在对象结构一致、行宽合理、无需大量填充时使用这种表格对齐。对象异构、参数复杂或新增长名称会制造大范围 diff 时，改为每项独立换行，不强求对齐。

避免用复杂匿名对象堆叠业务逻辑，也要留意 `{}` 可能选择 `std::initializer_list` 构造函数；不得机械迁移遗留初始化语法。

### 模板

短模板参数列表保持单行。需要换行时，第一个参数留在 `template <` 后，其余参数纵向对齐。

```cpp
template <typename Key,
		  typename Value,
		  std::size_t capacity>
class FixedMap
{
};
```

### 继承

单继承优先单行。多继承换行时使用行首冒号和行首逗号，并显式写访问权限。

```cpp
class RuntimeController final
	: public ControllerBase
	, public RuntimeObserver
{
};
```

虚继承写为 `public virtual Base`。`final` 位于类名之后、继承冒号之前。本 Skill 以 C++17 为基线，不使用 `concept` 或 `requires`。

## 7. 条件、循环、switch 与预处理指令

### 条件表达式

两个简单条件通常保持一行。

```cpp
const bool should_show = is_authorized && is_visible_node;
```

三个或更多条件也不因数量自动换行；应综合 120 列软上限、表达式复杂度和语义层次决定。换行时，顶层 `&&` 或 `||` 放在续行开头，并与赋值号 `=` 对齐。

```cpp
const bool should_refresh = has_config_changed
						  || has_runtime_changed
						  || needs_forced_refresh;
```

完整圆括号子表达式不拆开。

```cpp
const bool should_show = is_authorized && (is_public_node || is_included_node);
```

如果括号内容本身过长，提取具名布尔值，不要把小括号内部拆成多行。

```cpp
const bool is_supported_node = is_public_node || is_included_node || is_compatible_node;
const bool should_show = is_authorized && is_supported_node;
```

### 三目运算符

简单三目表达式在完整表达式清晰且不超过 120 列软上限时保持单行。不能仅因为输入原本已经拆行，就继续保留多行。

```cpp
const RuntimeState state = is_ready ? RuntimeState::Ready : RuntimeState::Idle;
```

确需多行时，`?` 和 `:` 各自在续行开头；两个续行的第一个非空白字符必须与赋值号 `=` 位于同一显示列。

`?`、`:` 行属于续行对齐，不是额外增加一级作用域缩进。先用 Tab 到达赋值号附近，再用最少空格精确对齐。

```cpp
const Visibility visibility = is_authorized && is_visible
							? Visibility::Shown
							: Visibility::Hidden;
```

如果左侧前缀太长，导致对齐空白过多，改用具名值或 `if...else`。禁止嵌套三目、带副作用的分支或复杂分支。

### 循环

只需要元素时使用范围 `for`；需要索引、相邻项、多个同步容器、起始偏移或步长时保留索引循环。

```cpp
for (const auto& node : nodes)
{
	refresh_node(node);
}
```

不要用带手工计数器的范围循环代替清晰的索引循环，也不要机械把普通 `for` 改成 `std::for_each`。

禁止空循环体：

```cpp
while (is_busy())
{
	// 硬件状态寄存器只能通过轮询等待。
}
```

### switch

每个 `case` 和 `default` 都建立独立作用域，默认使用展开形式。

```cpp
switch (state)
{
	case RuntimeState::Idle:
	{
		handle_idle();
		break;
	}

	case RuntimeState::Running:
	{
		handle_running();
		break;
	}

	default:
	{
		break;
	}
}
```

短分支仅包含以下内容时，可以使用单行作用域块：

- 一个简单操作，随后无条件 `break` 或 `return`；
- 仅有一个无条件 `break` 或 `return`。

```cpp
switch (state)
{
	case RuntimeState::Idle: { handler = &idle_handler; break; }
	case RuntimeState::Running: { handler = &running_handler; break; }
	default: { return false; }
}
```

分支包含局部变量声明、终止语句前有多个操作、嵌套控制流、注释、预处理指令或有意贯穿时，不能使用单行形式。连续的单行短分支之间不留空行；展开分支之间以及单行与展开分支的边界保留一个空行。短分支不再满足单行条件时必须立即展开。修改任一 `case` 后，仍需规范化完整 `switch`。

有意贯穿时使用 `[[fallthrough]];`。删除空 `else`，禁止静默的空 `catch`，不保留“以后再写”的空函数占位；可默认化的特殊成员函数使用 `= default`。

### 预处理指令

预处理指令的 `#` 始终位于物理行第 1 列，即使它位于 C++ 作用域内。普通 C++ 代码仍按作用域使用 Tab。

```cpp
void initialize_platform()
{
#if defined(_WIN32)
	initialize_windows();
#else
	initialize_posix();
#endif
}
```

宏能单行表达时保持单行。多行宏的非末行以 `\` 结尾，并保持结构一致。只有条件块很长、嵌套较深或 `#endif` 距离条件较远时才补充结束注释。

不要因为局部修改而重新格式化完整预处理区域，也不整理第三方或生成宏。

## 8. 类、头文件、源文件与职责分组

### 访问区段

访问区段按 `public`、`protected`、`private` 排列。访问标签不缩进，标签后不插入空行；后续访问区段前保留一个空行。

私有函数和私有数据分别使用独立的 `private:` 区段。删除空区段，`protected` 只用于真实扩展契约。

```cpp
class OptionController
{
public:
	explicit OptionController(DataManager& data_manager);
	void initialize();

protected:
	virtual void on_initialized();

private:
	void rebuild_items_();

private:
	DataManager& m_data_manager;
	bool m_initialized = false;
};
```

局部修改一个函数体时，不要求重排整个类。

### 职责分组

一个类或源文件包含多个职责时，使用简短中文名词分组，例如 `// 生命周期`、`// 页面操作`、`// 状态查询`。不要使用只重复访问权限的 `// 公共接口` 或 `// 私有函数`。

头文件中，分组注释紧贴第一条声明；源文件中，分组标题前后各保留一个空行。源文件定义顺序与头文件职责分组保持一致。

### include

`.cpp` 的 include 顺序：

1. 对应头文件；
2. 项目头文件；
3. 第三方头文件；
4. C 和 C++ 标准库头文件。

组间一个空行，组内不留空行。只有 include 区域属于当前改动时才整理，不依赖偶然的传递 include，也不机械改写 include 路径。

### 命名空间和文件

头文件和源文件作用域禁止 `using namespace`。新类型别名使用 `using`，并放在最小可用作用域。

命名空间采用 Allman 布局并带结束注释：

```cpp
namespace ui::option
{

class OptionController
{
};

} // namespace ui::option
```

新头文件使用 `#pragma once`，不同时添加宏保护。主要类型文件使用 PascalCase，例如 `OptionController.h` 和 `OptionController.cpp`。避免 `Utils.h`、`Common.cpp`、`Helper.h` 等含义模糊的文件名。

简单数据结构、枚举集合、模板、短小稳定内联函数和编译期常量适合放在头文件；复杂、经常变化、平台相关或依赖较重的实现放入源文件。

## 9. 命名规范

### 命名矩阵

| 对象 | 规则 | 示例 |
|------|------|------|
| 类、结构体、枚举、类型别名 | PascalCase | `RuntimeController`、`SaveResult` |
| 命名空间、命名空间别名 | snake_case | `ui::option_menu`、`runtime_ui` |
| 函数、参数、局部变量 | snake_case | `refresh_items`、`node_id` |
| 类非静态数据成员 | `m_` + snake_case | `m_current_index` |
| 私有辅助函数 | snake_case + `_` | `rebuild_items_` |
| 常量 | `k_` + snake_case | `k_invalid_index` |
| 枚举值 | PascalCase | `RuntimeState::Ready` |
| 宏 | UPPER_SNAKE_CASE | `OPTION_ENABLE_TRACE` |
| 类型模板参数 | PascalCase | `Key`、`Value` |
| 非类型模板参数 | snake_case | `capacity` |

简单局部模板允许 `T`、`U`、`K`、`V`。对外暴露的宏应带项目或模块前缀。

纯数据、配置或传输用途的 `struct` 字段使用普通 snake_case，不添加 `m_`；`class` 非静态成员使用 `m_`。外部协议、序列化字段和第三方接口名称保持原样。

```cpp
struct ThemeConfig
{
	std::string profile_id;
	int transition_ms = 0;
};
```

### 语义命名

命名表达业务意图，详细程度与作用域成正比。单位写入名称，例如 `timeout_ms`、`payload_bytes`。

布尔值使用正向语义，常见前缀包括 `is_`、`has_`、`can_`、`should_`、`needs_`、`supports_`。避免让 `true` 表示禁用、跳过或失败。

Getter 优先使用名词或查询语义，不添加无意义的 `get_`；Setter 使用 `set_`。布尔查询使用 `is_`、`has_`、`can_` 或 `should_`。

缩写按普通单词处理：`GpsModule`、`HttpServer`、`parse_json`，不写 `GPSModule` 或 `HTTPServer`。

避免在超出紧邻上下文的范围中使用 `data`、`info`、`obj`、`temp`、`value`、`flag` 等泛化名称，也不使用匈牙利命名法编码类型。

`i`、`j`、`it`、`tmp`、`value`、`result` 等临时名称只允许出现在短小、连续、语义单一的局部代码块中，并且声明、使用和结束都紧邻。它们不能跨业务阶段、分支或循环，也不能成为成员、全局量、静态量、长期参数、延迟捕获值、存储值或返回值。

## 10. 注释规范

### 语言和标点

新增源码注释默认使用简洁中文，技术标识符、协议名、JSON key、文件路径和第三方术语保留原文。

`//` 后保留一个空格。完整句子使用中文标点；职责标题和短枚举尾注不强制句号。

```cpp
// 必须在 View 创建完成后调用，否则无法同步当前焦点状态。
void bind_view(OptionView& view);
```

注释同样遵循 120 列软上限。需要换行时按语义拆分，每行都以 `// ` 开头；URL 可以例外。

### 写意图、约束和边界

注释用于解释代码无法清晰表达的内容，例如：

- 调用顺序；
- 所有权和生命周期；
- 非显然副作用；
- 返回值的特殊语义；
- 兼容和回退行为；
- 状态转换；
- 失败后的状态；
- 异步回调捕获原因。

```cpp
// 返回对象由 Registry 持有，调用方不得释放或跨配置重载长期缓存。
const RuntimeFact* find_fact(RuntimeFactId id) const;
```

不要注释明显操作：

```cpp
// 清空列表
m_items.clear();
```

类名不能明确表达边界时，在类声明前说明“负责什么”和“不负责什么”。头文件保存调用方需要知道的契约，源文件保存实现原因，不在两处重复完整说明。

### 职责标题

职责标题使用简短中文名词或名词短语，不使用装饰分隔线。

```cpp
// 状态更新
void rebuild_items_();
void refresh_item_state_();
```

### 强枚举

新建或实际修改的 `enum class` 中，每个枚举值都有简洁中文尾注。只在紧凑且不产生大量填充时对齐，优先使用 Tab。

```cpp
enum class SaveResult
{
	Success,		// 保存成功并完成状态同步
	InvalidValue,	// 输入值未通过约束校验
	CommandFailed	// 系统命令发送失败
};
```

尾注解释业务含义或差异，不直译枚举名；最后一个枚举值不加尾随逗号。

### TODO 和 FIXME

`TODO` 必须说明具体技术债和清理条件，有对应事项时附 issue 链接。

```cpp
// TODO: 协议版本全部升级到 v3 后，删除旧字段兼容分支。跟踪：#184
```

`FIXME` 只用于已知缺陷，并关联跟踪事项。

```cpp
// FIXME: 重连期间可能重复注册回调。跟踪：#219
```

不使用 `HACK`、`XXX` 等同义标签，不写“以后优化”“临时处理”等模糊 TODO，也不能未经用户同意把当前要求延期为 TODO。

## 11. 可读性与基础代码质量

### 函数流程

前置条件适合使用提前返回，以减少无意义嵌套；但不能为了单一出口或提前返回改变锁、事务、回滚和资源释放语义。

局部变量靠近首次使用处声明并立即初始化，作用域尽量小。不会重新赋值且具有明确语义的局部值使用 `const`。

普通业务逻辑尽量控制在约三层嵌套。只有职责可以独立命名、确实复用、隔离复杂条件或明显改善主流程时才提取辅助函数，不机械拆分一次性小函数。

### Lambda 和回调

Lambda 使用 Allman 花括号，不写单行 Lambda。捕获列表保持最小，默认避免 `[&]` 和 `[=]`。

异步、延迟执行或会被保存的回调不能按引用捕获可能提前销毁的局部变量。生命周期不明显时补充简洁中文说明。

```cpp
// 回调由任务队列延迟执行，node_id 必须按值捕获。
m_executor.post([this, node_id]()
{
	open_node_(node_id);
});
```

只有逻辑复用、具有独立职责、需要独立测试或严重遮蔽外层流程时，才把 Lambda 提取为具名函数。

### auto 和类型转换

右侧表达式能够清楚体现类型，或显式类型过长时使用 `auto`。业务枚举、结果类型、整数含义、裸指针和所有权不应被 `auto` 隐藏。

禁止 C 风格转换。普通显式转换使用 `static_cast`；`dynamic_cast`、`const_cast` 和 `reinterpret_cast` 只在其特定语义确实需要时使用，非显然的 `const_cast` 或 `reinterpret_cast` 必须说明原因。

### 所有权和参数传递

- `std::unique_ptr<T>` 表达独占所有权。
- `std::shared_ptr<T>` 只用于真实共享生命周期。
- `std::weak_ptr<T>` 用于不延长共享生命周期的观察关系。
- `T&` 表达必需的非拥有对象。
- `T*` 表达可选的非拥有对象。

不要因为生命周期不清楚就选择 `shared_ptr`，也不要机械把非拥有裸指针改成智能指针。函数只使用对象时，通常接收引用或指针，而不是调用方的智能指针包装。

小型值类型按值传递，大型只读对象使用 `const T&`，可变调用方对象使用 `T&`。所有权或生命周期无法从类型和名称看出时必须注释。

### 错误、返回值和日志

不要静默忽略有意义的成功、失败或事件消费结果。`bool` 只用于清晰的二元语义；调用方需要区分多种失败原因时使用强结果枚举。

失败路径必须保持状态一致。允许部分成功时，应明确记录保留了什么状态以及后续如何恢复。

日志应包含操作、对象或标识符、路径、实际值、期望范围或错误结果，避免只有 `failed`、`error`、`invalid value`。同一失败不要在多层重复记录，除非上层增加了业务上下文。

日志不能替代返回、回滚或停止成功路径。项目没有日志语言规范时，新业务日志默认使用中文，技术标识符保持原文。

不得在以返回值为主的项目中自行引入异常模型，也不能使用空 `catch` 或静默的 `catch (...)`。

### 遗留代码安全边界

修改遗留代码时，只整理当前任务实际触及的完整结构单元；不批量改名、不全文件格式化、不顺带转换编码或换行，也不修改第三方和生成内容。任何会扩大行为、架构或构建范围的调整都应作为独立任务讨论。

## 参考资料与差异说明

- [Google C++ Style Guide](https://google.github.io/styleguide/cppguide.html)：仅参考“规则、理由、正反例、例外”的表达方式。
- [search-before-build](https://github.com/user-w-ui/search-before-build)：参考中文 README 的信息组织方式。
- [winlean-coding-skill](https://github.com/ziguishian/winlean-coding-skill)：参考 Skill 仓库的文件层级与使用说明方式。

本 Skill 不继承上述项目的具体规则值。Tab、Allman 花括号、命名、注释语言和其他具体要求，以本仓库的运行时规则文件为准。
