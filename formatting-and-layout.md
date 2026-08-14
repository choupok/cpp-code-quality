# Formatting and Layout

## Baseline

Apply these rules to every complete structural unit within the allowed modification scope.

Do not imitate conflicting legacy formatting.

Do not format unrelated units solely to make the whole file uniform.

## Encoding and line endings

Use UTF-8 without BOM and LF for text files in this Skill repository.

For C++ repositories:

- create new C++ files as UTF-8 without BOM;
- follow an explicitly authorized repository newline convention, or use LF when no convention exists;
- preserve the encoding and newline style of existing C++ files;
- detect GBK, CP936, or other legacy encodings before writing Chinese text;
- require explicit user authorization before converting a complete file's encoding;
- do not batch-convert line endings during a local code change.

Visual Studio project and tool-generated files are exceptions, including:

- `.sln`;
- `.vcxproj`;
- `.vcxproj.filters`;
- `.vcxitems`;
- `.props`;
- `.targets`.

Preserve the existing or tool-generated encoding, BOM, newline style, XML indentation, and layout of those files. Do not force them to UTF-8 without BOM.

## Indentation

- Use tabs for block indentation.
- Each additional scope level adds one tab.
- Do not use spaces to represent block indentation.
- Prefer tabs for column alignment inside an allowed homogeneous table or compact enum.
- Use spaces only for the smallest unavoidable fine adjustment after tabs cannot reach the exact column.
- Do not use a long run of spaces where tabs can perform the alignment.
- Do not mix tabs and spaces to express block hierarchy inside one structural unit.
- Use a default tab display width of 4 unless an explicitly authorized project `.editorconfig` or `.clang-format` defines another width.
- Do not simulate a tab with four spaces.
- Blank lines must contain no tabs or spaces.
- Remove trailing whitespace.

Multi-column table alignment is allowed only within:

- one contiguous homogeneous initializer table;
- one compact enum with line-end comments.

Do not align ordinary declarations, assignments, or unrelated comments as a table. Do not align across blank lines, responsibility groups, or preprocessor branches. Abandon alignment when a long name would create excessive whitespace or a large unrelated diff.

This table-alignment restriction does not prohibit required continuation alignment for wrapped function calls, signatures, logical expressions, or ternary expressions.

## Braces

Use Allman braces for:

- functions;
- constructors and destructors;
- `if`, `else`, `for`, `while`, and `do`;
- `switch`;
- classes and structs;
- enums;
- namespaces;
- lambdas.

Always use braces for control-flow bodies, including one-statement bodies.

```cpp
if (!is_ready)
{
	return;
}
```

Do not preserve attached braces inside a modified structural unit.

## Blank lines

- Keep exactly one blank line between function definitions.
- Do not use two or more consecutive blank lines.
- Do not add an empty line immediately inside an opening or closing brace.
- Use one blank line between responsibility groups.
- Use one blank line between meaningful stages inside a function when it improves reading.
- Do not insert blank lines mechanically after every few statements.

Typical function stages are:

1. precondition checks;
2. data lookup or preparation;
3. core processing;
4. state commit or view refresh.

Short functions do not need artificial stages.

## Horizontal whitespace

- Put one space between a control keyword and `(`, such as `if (` and `for (`.
- Do not add padding immediately inside parentheses.
- Put one space on both sides of assignment, arithmetic, comparison, logical, and ternary operators.
- Do not put a space between a unary operator and its operand.
- Put no space before a comma and one space after it.
- Put no space before a semicolon. In a `for` header, put one space after each semicolon.
- Put no spaces around `.`, `->`, or `::`.
- Bind pointer and reference symbols to the type: `Type*`, `Type&`, and `const Type&`.

```cpp
if (is_ready && count > 0)
{
	for (int index = 0; index < count; ++index)
	{
		items[index].refresh();
	}
}
```

Declare one ordinary variable per line. Do not combine pointer or reference declarations.

Structured bindings and tightly coupled control variables in a `for` initializer are exceptions.

Do not batch-rewrite unrelated legacy declarations.

## Expressions and function calls

Keep expressions and calls on one line when they remain clear.

Do not split merely because:

- there are three or four arguments;
- the expression contains `&&` or `||`;
- a function name is moderately long;
- a visual pattern would look more symmetrical.

```cpp
const bool is_visible = check_primary_condition() && check_secondary_condition();
apply_runtime_state(node, visible_nodes, enabled_nodes);
```

Split only when a single line is clearly difficult to read because of length, complex argument expressions, or distinct semantic layers.

Use 120 visible columns as a soft limit, not a mandatory wrap point.

Allow exceptions for:

- URLs;
- strings that cannot be safely split;
- include directives;
- generated content;
- content preserved by an explicitly authorized project rule.

Do not create excessive alignment whitespace merely to stay under the soft limit. Extract a semantic value or rewrite the flow when wrapping would be less readable.

When a call must be split, keep the first argument on the opening line and vertically align subsequent arguments with it. Keep the closing parenthesis after the last argument.

```cpp
register_runtime_handler(RuntimeFactId::GpsAvailable,
						 create_fact_handler(runtime_registry, event_bus),
						 RuntimeUpdatePolicy::NotifyOnChange);
```

Use tabs for block indentation and prefer tabs for continuation alignment. Add only the minimum spaces needed when tabs cannot reach the exact target column.

Prefer enums, option objects, or named local variables over call-site comments for unclear arguments.

For an external or legacy API that cannot be changed, an argument annotation may use the exact parameter identifier:

```cpp
send_command(command, /*timeout_ms=*/500, /*retry_count=*/2);
```

Do not annotate ordinary self-explanatory parameters. Do not translate or invent the parameter name, and do not use annotations to hide an interface with too many unclear arguments.

## Function declarations and definitions

Prefer a single-line signature when it is clear.

Do not use parameter count as an automatic wrapping rule.

When wrapping is necessary:

- keep the first parameter on the same line as the function name;
- align later parameters vertically with the first parameter;
- keep the closing parenthesis after the final parameter;
- use the same wrapping structure in declaration and definition.

```cpp
bool build_runtime_menu(const ProductConfig& product_config,
						const RuntimeFactRegistry& runtime_fact_registry,
						const PermissionSession& permission_session);
```

Default arguments appear only in declarations, not definitions.

Use a stable declaration-specifier order.

- Put attributes at the beginning of a declaration.
- Put `explicit` at the beginning of a constructor declaration.
- Prefer stable prefix order such as `inline static constexpr` when those specifiers are combined.
- Order member suffixes as `const` or a reference qualifier, then `noexcept`, then `override`, then `final`.
- Use `override` for an override and omit redundant `virtual`.
- When both apply, write `override final`.
- Put `= default`, `= delete`, and `= 0` at the end.

```cpp
[[nodiscard]] virtual SaveResult save() const noexcept = 0;
void refresh() noexcept override final;
explicit OptionController(DataManager& data_manager);
```

Use:

```cpp
const ProductConfig& config;
OptionFacade* facade;
const char* file_name;
```

Do not introduce east-const or pointer-binding variants into new code unless an explicitly authorized project rule requires them.

## Return statements

Keep a simple return on one line. Do not use `return(...)` or add unnecessary parentheses.

Keep a simple, side-effect-free ternary return on one line. For a complex return, prefer semantic named values or early returns.

Follow the initializer-list rules for braced returns.

```cpp
return { node_id, RuntimeState::Ready };
```

Do not create a meaningless local `result` merely to return it immediately. Keep the local only when its name, validation, logging, or staged processing adds meaning.

## Constructors and initializer lists

Keep constructor parameters on one line when the parameter list is short and clear.

```cpp
OptionController::OptionController(DataManager& data_manager, OptionFacade& option_facade)
	: m_data_manager(data_manager)
	, m_option_facade(option_facade)
{
}
```

Wrap constructor parameters only when types or the complete signature are clearly too long.

Whenever an initializer list exists:

- place every initializer on its own line;
- use leading commas;
- keep initializer order identical to member declaration order;
- do not repeat declaration-site defaults unless the constructor intentionally overrides them.

```cpp
Foo::Foo(DataManager& data_manager)
	: m_data_manager(data_manager)
	, m_runtime_state(RuntimeState::Idle)
{
}
```

For braced initialization:

- put one space inside a non-empty single-line initializer: `{ value1, value2 }`;
- write an empty initializer as `{}`;
- use Allman layout for a multiline initializer;
- put one item per line in an ordinary multiline initializer;
- do not add a trailing comma to the final item.

```cpp
const Point origin { 0, 0 };
const RuntimeOptions options {};

const std::vector<int> values =
{
	1,
	2,
	3
};
```

For multiple homogeneous objects in one contiguous vector, map, or similar initializer, each object may stay on one line. Align equivalent parameter positions vertically, preferring tabs and using spaces only for minimal fine adjustment.

```cpp
const std::vector<MenuItem> items =
{
	{ ItemId::Home,		"home",		true  },
	{ ItemId::Settings,	"settings",	false },
	{ ItemId::About,		"about",		true  }
};
```

Do not use table alignment when the objects are heterogeneous, expressions are complex, lines become too long, or alignment would create excessive whitespace or broad diff churn.

Avoid complex anonymous objects and ambiguous `initializer_list` construction. Do not mechanically migrate unrelated legacy initialization syntax.

## Templates and inheritance

Keep a short template parameter list on one line.

When wrapping is necessary, keep the first parameter after `template <` and vertically align the remaining parameters.

```cpp
template <typename Key,
		  typename Value,
		  std::size_t capacity>
class FixedMap
{
};
```

Keep a single inheritance clause on one line when clear. For wrapped multiple inheritance, use a leading colon and leading commas like a constructor initializer list.

```cpp
class RuntimeController final
	: public ControllerBase
	, public RuntimeObserver
{
};
```

Write inheritance access explicitly. Write virtual inheritance as `public virtual Base`. Put `final` after the class name and before the inheritance colon.

This Skill targets C++17. Do not introduce `concept` or `requires`.

## `switch` and `case`

Every `case` and `default` has an independent scope.

Use the expanded form by default.

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

A short branch may use a single-line scoped block when it contains only:

- one simple operation followed by an unconditional `break` or `return`; or
- one unconditional `break` or `return`.

```cpp
switch (state)
{
	case RuntimeState::Idle: { handler = &idle_handler; break; }
	case RuntimeState::Running: { handler = &running_handler; break; }
	default: { return false; }
}
```

Do not use the single-line form when the branch contains:

- a local variable declaration;
- more than one operation before the terminating statement;
- nested control flow;
- a comment or preprocessor directive;
- intentional fallthrough.

Rules:

- indent `case` and `default` one tab inside `switch`;
- add braces for every case regardless of local variables;
- omit blank lines between consecutive single-line short branches;
- leave one blank line between expanded branches and at boundaries between short and expanded branches;
- expand a short branch as soon as it no longer meets the single-line conditions;
- normalize the complete `switch` when any case is substantively modified.

Use full Allman layout for compound control flow.

- Put `else`, `else if`, `catch`, and the `while` of `do...while` on their own lines.
- Do not insert a blank line between the preceding `}` and that keyword.
- Do not use attached forms such as `} else {`, `} catch`, or `} while`.

Use `[[fallthrough]];` for intentional fallthrough.

Do not keep an empty `else`, a silent empty `catch`, or an empty placeholder function for future work. Use `= default` for a defaultable special member function.

## Header declaration grouping

Group declarations by responsibility.

Order access sections as:

1. `public`;
2. `protected`;
3. `private`.

Do not indent access labels. Put no blank line immediately after an access label. Put one blank line before each subsequent access section.

Use `protected` only for a real extension contract. Remove empty access sections.

Do not insert a blank line between a responsibility comment and its first declaration.

Do not insert an extra blank line between an access label and the first group.

Use a separate `private:` section for data members.

Use a separate `private:` section for private functions and another for private data members.

```cpp
class OptionController
{
public:
	OptionController(DataManager& data_manager, OptionFacade& option_facade);

	// 生命周期
	void initialize();
	void reset();

	// 页面操作
	void open_option();
	void close_option();

	// 状态查询
	bool is_available() const;

private:
	// 配置处理
	void load_config_();
	void apply_rules_();

	// 状态更新
	void rebuild_items_();
	void refresh_item_state_();

private:
	// 依赖对象
	DataManager& m_data_manager;
	OptionFacade& m_option_facade;

	// 运行状态
	bool m_initialized = false;
	int m_current_index = 0;
};
```

A single obvious responsibility does not need a redundant group title.

Do not add titles such as `// 公共接口` or `// 数据成员` when they add no information beyond the access label.

## Source definition grouping

Within one class, order source definitions consistently with the header's responsibility groups.

Recommended order:

1. constructors and destructor;
2. lifecycle;
3. public business operations;
4. external event entry points;
5. state queries;
6. private business processing;
7. private state updates;
8. private helpers.

A constructor or destructor at the beginning does not need a `// 构造与析构` title.

For source responsibility titles:

- leave one blank line before the title;
- leave one blank line after the title;
- keep one blank line between functions in the same group.

```cpp
OptionController::OptionController(DataManager& data_manager)
	: m_data_manager(data_manager)
{
}

// 生命周期

void OptionController::initialize()
{
}

void OptionController::reset()
{
}

// 页面操作

void OptionController::open_option()
{
}
```

Do not reorder a whole class when only one function body changed.

Reorder related declarations and definitions when the current change adds or restructures interfaces and the grouping would otherwise become inconsistent.

## Include layout

When the include block is part of the current change, use this order.

For a `.cpp` file:

1. matching header;
2. project headers;
3. third-party headers;
4. C and C++ standard-library headers.

```cpp
#include "OptionController.h"

#include "OptionFacade.h"
#include "UiRuntimeFacade.h"

#include <lvgl.h>
#include <nlohmann/json.hpp>

#include <algorithm>
#include <string>
#include <vector>
```

For a header:

1. project headers required by declarations;
2. third-party headers;
3. C and C++ standard-library headers.

Rules:

- separate groups with exactly one blank line;
- do not add blank lines within a group;
- sort directly modified entries consistently within their group;
- do not reorder the include block merely because a function body changed;
- do not automatically rewrite include paths;
- do not remove common or precompiled headers solely to enforce this baseline;
- remove an include only when it is clearly unused and removal can be validated.

Headers should directly include the complete standard-library and complete-type dependencies required by their own declarations.

Prefer safe forward declarations for project types used only through pointers or references.

Do not rely on accidental transitive includes.

Do not mechanically forward-declare templates or third-party types when a complete definition is required.

## Namespaces, forward declarations, and `using`

Do not use `using namespace` in headers or at source-file scope.

Keep standard-library qualification such as `std::string` and `std::vector`.

Use `using` for new type aliases, not `typedef`.

Keep `using` declarations in the smallest useful scope. Do not add file-global aliases merely to save a few characters.

Place forward declarations after includes and before the namespace or type definitions. Keep them together rather than scattering them through the file.

Use Allman layout for namespaces.

Always add a namespace-closing comment.

```cpp
namespace ui
{

class OptionController
{
};

} // namespace ui
```

For nested namespace syntax already used by the module:

```cpp
namespace ui::option
{

class OptionController
{
};

} // namespace ui::option
```

For an anonymous namespace:

```cpp
namespace
{

constexpr int k_invalid_index = -1;

} // namespace
```

This Skill does not independently add, remove, rename, or redesign namespaces.

Use an existing module namespace for new code only when the module has an explicit and stable namespace convention.

A new module without a defined convention does not receive an invented namespace from this Skill.

Use anonymous namespaces for source-file-private free functions, constants, aliases, and helper types when appropriate.

## Basic naming

Use these rules for new or substantively modified identifiers:

- types, classes, structs, and enums: `PascalCase`;
- type aliases: `PascalCase`;
- namespaces and namespace aliases: `snake_case`;
- functions, parameters, and local variables: `snake_case`;
- class non-static data members: `m_` prefix with `snake_case`;
- fields of pure data, configuration, or transfer structs: plain `snake_case` without `m_`;
- private helper functions: trailing `_`;
- constants: `k_` prefix with `snake_case`;
- enum values: `PascalCase`;
- macros: `UPPER_SNAKE_CASE`, with a project or module prefix for exported macros;
- type template parameters: `PascalCase`;
- non-type template parameters: `snake_case`;
- boolean names: semantic forms such as `is_`, `has_`, `can_`, `should_`, `needs_`, or `supports_`;
- containers: names that express collection meaning, such as `items`, `node_ids`, or `handlers`.

Allow `T`, `U`, `K`, and `V` for simple local templates.

Preserve externally defined protocol, third-party, and serialized field names.

Prefer a noun or query phrase for a getter without a meaningless `get_`. Use `set_` for a setter. Use `is_`, `has_`, `can_`, or `should_` for boolean queries.

Treat acronyms as ordinary words: `GpsModule`, `HttpServer`, and `parse_json`.

Choose names for business intent, with detail proportional to scope. Put units in names such as `timeout_ms` and `payload_bytes`.

Use positive boolean names. Avoid generic names such as `data`, `info`, `obj`, `temp`, `value`, and `flag` outside immediate context. Do not encode types with Hungarian notation.

Allow short temporary names such as `i`, `j`, `it`, `tmp`, `value`, or `result` only inside a short, continuous, semantically single local block where declaration, use, and lifetime are immediate.

Do not use such temporary names across business stages, branches, or loops, or as members, globals, statics, long-lived parameters, delayed captures, stored values, or returned values.

Do not use unclear abbreviations merely to shorten code.

Do not rename unrelated legacy identifiers in a normal implementation task.

Do not copy obviously inconsistent legacy naming into new code.

Do not use this Skill to decide architectural suffixes such as Manager, Service, Facade, Registry, Resolver, Builder, Request, Spec, or Context.

## Macros, constants, enums, and static symbols

Put the `#` of every preprocessor directive in physical column 1, including directives inside a C++ scope. Indent ordinary C++ statements according to their scope.

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

Keep a macro on one line when practical. For a multiline macro, end every non-final line with `\` and keep its structure consistent.

Add an `#endif` comment only when the conditional region is long, nested, or far from its opening directive.

Do not reformat an entire preprocessor region for a local change. Preserve third-party and generated macro layout.

Do not introduce macros for ordinary constants, functions, or simple predicates.

Prefer:

```cpp
constexpr int k_invalid_index = -1;

constexpr bool is_valid_index(int index)
{
	return index >= 0;
}
```

Use macros only when preprocessing is genuinely required, including:

- conditional compilation;
- compile-time platform selection;
- required registration or code-generation macros;
- third-party APIs that require macros.

Do not batch-remove legacy macros during unrelated work.

Use the smallest sufficient visibility:

- function-only constant: local `constexpr`;
- source-file-only constant or free helper: anonymous namespace;
- class constraint: `static constexpr`;
- cross-file shared constant: defer ownership to architecture/module design.

Prefer `constexpr` for compile-time constants and `const` for runtime-initialized immutable values.

Do not introduce file-scope `static` free functions or variables when an anonymous namespace is appropriate.

Prefer strong enums:

```cpp
enum class SaveResult
{
	Success,       // 保存成功并完成状态同步
	InvalidValue,  // 输入值未通过约束校验
	CommandFailed  // 系统命令发送失败
};
```

Strong-enum rules:

- one enumerator per line;
- no trailing comma on the final enumerator;
- every enumerator has a concise Chinese line-end comment;
- comments explain business intent or semantic distinction, not a literal translation;
- align line-end comments when it remains readable;
- do not repeat the enum type name in enumerator names;
- use explicit bit values only for real flag sets.

When an enum is substantively modified, review the complete enum and all enumerator comments.

## Member initialization

Initialize simple default state at the member declaration.

```cpp
bool m_initialized = false;
int m_current_index = 0;
OptionFacade* m_option_facade = nullptr;
```

Use constructor initializer lists for:

- references;
- `const` members;
- non-default-constructible members;
- dependencies established by constructor parameters;
- intentionally non-default object state.

Do not duplicate a declaration-site default in every constructor.

Use `nullptr`, not `NULL` or `0`, for pointers.

For simple aggregate structs, use member defaults instead of a constructor that only assigns default values.

Do not mechanically replace clear values such as `false`, `0`, and `nullptr` with `{}`.

Do not batch-migrate unrelated legacy classes to a new initialization style.

## Function-body readability

Prefer precondition checks and early returns when they reduce nesting.

Do not force a single exit when it makes the function harder to read.

Do not change resource-release, lock, transaction, or rollback semantics merely to introduce early returns.

Declare local variables near first use and initialize them at declaration.

Keep variable scope as small as practical.

Use `const` for meaningful local values that are not reassigned.

Do not create a temporary variable that only forwards a result without adding semantic value.

Good:

```cpp
const bool should_refresh = has_config_changed || has_runtime_changed;
if (!should_refresh)
{
	return;
}
```

Avoid:

```cpp
const bool result = validate_config();
if (result == false)
{
	return;
}
```

Keep ordinary business logic around three nesting levels when practical.

Use early return or `continue` to reduce meaningless nesting.

Do not mechanically extract tiny one-use helper functions merely to shorten a function.

A helper should provide at least one of:

- an independently nameable responsibility;
- reuse;
- isolation of a complex condition;
- isolation of error handling or platform variation;
- a clear improvement to the main flow.

## Lambdas and callbacks

Use Allman braces for lambdas. Do not use one-line lambdas.

A simple forwarding lambda may remain directly in a call.

A complex lambda may also remain inline when all of these are true:

- it is used only at that call site;
- it is tightly coupled to the surrounding registration, traversal, or callback context;
- extraction would create a private member used only once without an independent responsibility;
- captures and lifetime are clear;
- the inline form remains readable and does not create deeply nested callback logic.

Do not extract a lambda solely because it has multiple statements or checks.

Extract a named function when at least one applies:

- the logic is reused;
- it has a stable independent business responsibility;
- it needs independent testing;
- it obscures the outer control flow;
- it contains complex state transitions, recovery, resource lifetime, or multiple nested flows.

Keep captures minimal.

Avoid broad `[&]` and `[=]` captures by default.

Use `[this]` for member access.

Do not capture a local variable by reference when the callback may be asynchronous, delayed, or stored.

When lifetime or capture reasoning is not obvious, add a concise Chinese comment.

Named stored lambdas use business-semantic names, not `callback`, `func`, or `lambda` unless the context makes the meaning unmistakable.

## Conditions and boolean expressions

Use real booleans directly:

```cpp
if (m_initialized)
{
}

if (!is_feature_enabled())
{
}
```

Do not write `== true` or `== false`.

Compare raw and smart pointers explicitly with `nullptr`.

```cpp
if (node == nullptr)
{
	return;
}
```

Do not treat integers, enums, or status codes as implicit booleans. Compare them to the actual semantic value.

Keep two simple conditions on one line.

```cpp
const bool should_show = is_authorized && is_visible_node;
```

Do not wrap mechanically based only on condition count. For three or more terms, consider the 120-column soft limit, expression complexity, and semantic grouping.

When a logical expression must wrap, put each top-level `&&` or `||` at the beginning of a continuation line and align it vertically with the assignment `=`.

```cpp
const bool should_refresh = has_config_changed
						  || has_runtime_changed
						  || needs_forced_refresh;
```

Do not mix leading and trailing operator styles inside one expression.

When `&&` and `||` are mixed, use parentheses to make grouping explicit. Keep the complete parenthesized subexpression on one line.

```cpp
const bool should_show = is_authorized && (is_public_node || is_included_node);
```

If a parenthesized subexpression is too long, extract it into a semantic boolean instead of splitting the content inside the parentheses.

```cpp
const bool is_supported_node = is_public_node || is_included_node || is_compatible_node;
const bool should_show = is_authorized && is_supported_node;
```

Extract a condition into a boolean variable only when the variable adds business meaning.

Avoid complicated negated expressions. Split them into clear early returns when that improves readability.

Use ternary expressions only for simple, side-effect-free two-way value selection.

Keep a simple ternary on one line when the complete expression stays readable and within the 120-column soft limit. Do not preserve a multiline ternary merely because the input was already split.

```cpp
const RuntimeState state = is_ready ? RuntimeState::Ready : RuntimeState::Idle;
```

When a ternary must wrap, put `?` and `:` at the beginning of their own continuation lines. The first non-whitespace character on each continuation line must occupy the same display column as the assignment `=`.

The `?` and `:` lines are continuation alignment, not one additional block-scope indentation level. Reach the assignment column with tabs first and then the minimum number of spaces.

```cpp
const Visibility visibility = is_authorized && is_visible
							? Visibility::Shown
							: Visibility::Hidden;
```

When the left prefix is too long or alignment would be excessive, rewrite with a semantic named value or `if...else`.

Do not use nested ternary expressions, complex branches, or a ternary expression that selects between side-effecting calls.

## Loops and containers

Use range-based `for` when only elements are needed.

```cpp
for (const auto& node : nodes)
{
	refresh_node(node);
}
```

Use `auto&` when modifying elements.

Use by-value iteration only for small value types when a copy is intended.

Keep an index-based loop when the business logic needs the index, adjacent elements, multiple synchronized containers, a start offset, or a step.

Do not replace a clear index loop with a range loop plus a manually maintained counter.

Use `std::size_t` for container indices by default.

When an external interface requires `int`, perform a clear conversion at the interface boundary and avoid repeated signed/unsigned mixing.

Do not use C-style casts.

Use `continue` to reduce unnecessary nesting when it improves clarity.

Keep loop-local variables inside the loop unless their values are needed afterward.

Do not erase from a container through an invalidating range-based loop.

Use a safe iterator pattern or an appropriate standard algorithm.

Do not mechanically replace a clear `for` loop with `std::for_each`.

Use `empty()` for empty-container checks.

Do not use an empty loop body such as `while (condition);`. Use braces and a concise reason comment when an empty loop is intentional.

Do not leave an empty `else` branch. Do not use a silent empty `catch`. Do not keep empty future placeholders.

## `auto` and casts

Use `auto` when the right-hand expression makes the type clear or the explicit type is excessively long.

Good uses include:

- iterators;
- lambdas;
- template-heavy results;
- clearly named factories or queries.

Prefer explicit types when the type itself contributes business meaning:

```cpp
const RuntimeState state = resolve_runtime_state();
const SaveResult result = save_setting(value);
const int current_index = get_current_index();
const bool is_visible = resolve_visibility(node);
```

Do not use `auto` to hide:

- a raw pointer;
- reference semantics;
- smart-pointer ownership;
- an important business enum or result type.

`auto*` is acceptable when the explicit type is already visible in a template expression:

```cpp
const auto* payload = event.get<ParamChangedPayload>();
```

Use `auto&` or `const auto&` when retrieving a container element by reference to avoid accidental copies.

Do not use C-style casts.

Prefer `static_cast` for explicit numeric, enum, inheritance, and `void*` restoration conversions.

Use `dynamic_cast`, `const_cast`, and `reinterpret_cast` only when their specific semantics are required.

A non-obvious `const_cast` or `reinterpret_cast` requires a concise Chinese reason comment.

Validate integer input before converting it to a strong enum unless a higher layer has already guaranteed the range.

Avoid `auto value = { 1 };` when it could unexpectedly infer an `initializer_list`.

## Ownership and parameter passing

Use types to express ownership:

- `std::unique_ptr<T>`: exclusive ownership;
- `std::shared_ptr<T>`: real shared lifetime;
- `std::weak_ptr<T>`: observation without extending shared lifetime;
- `T&`: required non-owning object;
- `T*`: optional non-owning object.

Do not choose `shared_ptr` merely because lifetime is unclear.

Prefer `unique_ptr` for newly created exclusive ownership.

Use `weak_ptr` to break shared-ownership cycles.

Pass small value types by value.

Pass large read-only objects as `const T&`.

Pass mutable caller-owned objects as `T&`.

Use a pointer for an optional non-owning parameter and a reference for a required non-owning parameter.

Parameter types should express whether a function acquires ownership.

A function that takes ownership of a `unique_ptr` or intentionally stores shared ownership may take the smart pointer by value and move it.

A function that only uses the object should normally accept `T&`, `const T&`, `T*`, or `const T*`, not the caller's smart-pointer wrapper.

Raw pointers are valid for non-owning observation, C APIs, framework callbacks, registry-owned objects, and optional parameters.

Do not mechanically replace raw pointers with smart pointers.

After `std::move`, do not depend on the moved-from value except for operations explicitly guaranteed by its type.

Non-obvious ownership and lifetime semantics require comments according to `comments-and-documentation.md`.

## Error handling, return values, and logs

Do not silently ignore a meaningful success, failure, or handling result.

Do not add `(void)function();` merely to suppress a warning.

Use `bool` only for a clear binary result such as success/failure, found/not found, or handled/not handled.

Use a strong result enum when callers need distinct failure reasons.

Do not use multiple boolean output parameters to encode result variants.

Keep failure paths state-consistent.

Do not leave a partially updated state unless partial success is intentional and documented.

Prefer early returns for clear error paths.

Logs must provide useful context such as:

- operation;
- object or identifier;
- path;
- actual value;
- expected range;
- error result.

Avoid logs such as `error`, `failed`, or `invalid value` without context.

Avoid duplicate logs for the same failure at multiple layers unless the upper layer adds meaningful business context.

Do not log normal optional misses, idempotent ignores, user cancellation, or configuration-disabled behavior as errors.

Logging does not replace returning, rolling back, or stopping the success path.

Follow an explicit project log-language rule when present. Otherwise, new business logs default to Chinese while technical identifiers remain unchanged.

Do not introduce an exception-based error model into a project that uses return values unless the task explicitly changes that architecture.

Do not swallow exceptions with an empty `catch` or silent `catch (...)`.

## Header guards, file names, and file organization

New C++ headers use:

```cpp
#pragma once
```

Do not add both `#pragma once` and a macro guard.

Do not batch-convert existing macro guards during unrelated changes.

Use this header order:

1. header guard;
2. includes;
3. forward declarations;
4. namespace;
5. type definitions.

Do not place long ordinary function implementations, large business flows, or movable static-data definitions in headers.

Use a principal type's `PascalCase` name for its header and source file:

- `OptionController.h`
- `OptionController.cpp`
- `RuntimeFactRegistry.h`
- `RuntimeFactRegistry.cpp`

Related data types may share a clear responsibility-based file such as:

- `ThemeConfigTypes.h`
- `RuntimeFactTypes.h`

Do not introduce vague file names such as:

- `Utils.h`
- `Common.cpp`
- `Helper.h`
- `Misc.h`
- `NewController.cpp`

A file should express one clear responsibility, but do not mechanically enforce one type per file.

A header-only file is appropriate for:

- simple data structures;
- enum collections;
- templates;
- short stable inline functions;
- compile-time constants or type aliases.

Move complex, frequently changing, platform-dependent, or dependency-heavy implementation into a source file.

Only keep a function inline in a header when it is short, stable, and free of complex branches or implementation dependencies.

Organize a `.cpp` file as:

1. matching header;
2. remaining include groups;
3. anonymous namespace;
4. constructors and destructor;
5. responsibility-grouped definitions.

This Skill may identify excessive file responsibilities, but it does not automatically split files, move classes across modules, or edit build-system files. Those changes belong to architecture/refactoring work.
