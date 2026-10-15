# Formatting Golden Cases（人工回归样例）

这些示例用来验证规范解释的一致性，**不是**独立代码规范、自动化测试程序或可直接编译的完整源码。没有 `PASS` 声明时，不代表某个 Agent 已执行过回归。核对修改后的完整结构单元，并遵守 `SKILL.md` 的最小修改边界。

## FMT-NS-001：非空 namespace 的空行例外

**期望**：namespace 内侧恰好一行空行；一般函数不允许套用。

```cpp
namespace ui
{

class Controller
{
};

} // namespace ui

void reset()
{
	refresh();
}
```

**不允许**：把 `void reset()` 的 `{` 后也加一个“对称空行”，或删除 namespace 内侧的规范空行。

## FMT-SWITCH-001：短 case 是允许项，不是强制压缩

**允许**（保持已有完整展开形式也合法）：

```cpp
switch (state)
{
	case State::Idle: { reset(); break; }
	case State::Busy: { return false; }
}
```

**必须展开**（声明局部变量）：

```cpp
switch (state)
{
	case State::Idle:
	{
		const int result = reset();
		consume(result);
		break;
	}
}
```

**不允许**：将包含局部变量、预处理指令、注释或嵌套流程的 case 压成一行。

## FMT-INIT-001：普通初始化与同质表格

**普通初始化**（内侧恰好一个空格）：

```cpp
const Point origin { 0, 0 };
const RuntimeOptions options {};
```

**允许同质表格对齐**（填充只限同一连续表格）：

```cpp
const std::vector<MenuItem> items =
{
	{ ItemId::Home,		"home",		true  },
	{ ItemId::Settings,	"settings",	false }
};
```

**不允许**：把表格行末额外空格照搬到单个 `Point` 对象，或跨空行、不相干的初始化集合做列对齐。

## FMT-WRAP-001：单行优先，参数数量不是触发器

**期望单行**：

```cpp
bool save_setting(int item_id, int value, bool notify);
save_setting(item_id, value, true);
```

**允许长签名续行**（真正过长时首参数留在开头）：

```cpp
bool build_runtime_menu(const ProductConfig& product_config,
						const RuntimeFactRegistry& runtime_fact_registry,
						const PermissionSession& permission_session);
```

**不允许**：只因“有三个参数”或“接近 120 列”就拆开清晰的调用。

## FMT-CONT-001：无赋值条件的续行锚点

**期望**：

```cpp
if (is_ready
	&& has_permission
	&& needs_refresh)
{
	refresh();
}
```

**有赋值时**使用 `=` 对齐策略，见 [Conditions](../formatting-and-layout.md#conditions-and-boolean-expressions)。**不允许**：把无 `=` 的 `if` 套到赋值对齐算法中，或混用行首 / 行尾操作符。

## FMT-LAMBDA-001：调用中的 Lambda 仍采用 Allman

**期望**：

```cpp
register_handler(Event::Ready, [this](const Event& event)
{
	on_ready(event);
});
```

**不允许**：为追求调用紧凑把它改成 `[this](const Event& e) { on_ready(e); }`。多语句 Lambda 是否提取成命名函数由职责和生命周期决定，不能只按行数判断。

## FMT-MACRO-001：预处理指令在第一列

**期望**：

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

**不允许**：把 `#if` 缩进到块内列，或把分支中的 `initialize_windows();` 左移到第一列。

## 回归检查方式

对每个涉及的结构：确认命中相应规则与例外 → 对照“允许 / 必须 / 不允许” → 检查最终 Diff 只修改授权范围。修复真实排版失误时，优先用精简的真实形态补充本文件，避免只给出新的抽象口号。
