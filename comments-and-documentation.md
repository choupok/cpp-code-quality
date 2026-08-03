# Comments and Documentation

## Purpose

Comments explain intent, boundaries, constraints, ownership, and reasons that the code cannot express clearly by itself.

Comments do not exist to restate syntax or to create visual noise.

Apply these rules together with `formatting-and-layout.md`.

## Language

New source comments default to Chinese.

Keep technical identifiers in their original form, including:

- type names;
- function names;
- JSON keys;
- protocol names;
- file paths;
- third-party terms.

When an explicitly authorized project rule requires another comment language, follow that rule.

Use Chinese punctuation for complete Chinese sentences.

Do not force terminal punctuation on short responsibility labels or concise enum line-end comments.

Preserve technical identifiers exactly rather than translating them.

## Comment form

Prefer `//` comments.

Put exactly one space after `//` for ordinary comments.

Use a single line when one line is sufficient.

Use consecutive `//` lines for a short multi-line explanation.

```cpp
// 负责解析产品配置并生成运行态菜单结构。
// 不负责执行 Setting 保存和系统命令发送。
```

Do not introduce decorative separators.

Avoid:

```cpp
// ============================================================
// 生命周期
// ============================================================
```

Use:

```cpp
// 生命周期
```

Do not require Doxygen syntax unless an explicitly authorized project rule requires it.

Use 120 visible columns as a soft limit for comments. When wrapping is needed, split at a semantic boundary and begin every line with `// `. URLs may exceed the soft limit.

## Structural comments

Structural comments organize a file by responsibility.

They are mandatory when a class or source file contains multiple distinct responsibility groups.

### Header groups

Place the responsibility comment immediately before the first declaration with no blank line between them.

```cpp
public:
	// 生命周期
	void initialize();
	void reset();

	// 页面操作
	void open_option();
	void close_option();
```

Use short Chinese nouns or noun phrases.

Good examples:

- `// 生命周期`
- `// 页面操作`
- `// 外部事件`
- `// 状态查询`
- `// 配置处理`
- `// 状态更新`
- `// 会话管理`
- `// 依赖对象`
- `// 运行状态`

Do not add a redundant group title when one obvious responsibility contains only a small number of declarations.

Avoid titles that merely repeat an access label, such as:

- `// 公共接口`
- `// 私有函数`
- `// 数据成员`

unless the title carries additional domain meaning.

### Source groups

For `.cpp` responsibility sections:

- leave one blank line before the title;
- leave one blank line after the title;
- keep the title directly associated with the following function group.

```cpp
void OptionController::reset()
{
}

// 页面操作

void OptionController::open_option()
{
}
```

A constructor or destructor at the start of a source file does not need a `// 构造与析构` title.

Only one obvious source responsibility does not require a title.

## Class responsibility comments

Add a class responsibility comment when the class name alone does not make its boundary clear.

Typical triggers:

- the class could be confused with another Manager, Service, Facade, Resolver, or Registry;
- the class coordinates multiple components but owns only part of the workflow;
- the class separates configuration description from runtime execution;
- lifecycle or ownership constraints are important;
- the class is the module's external entry point;
- the class adapts or transforms between domains.

Example:

```cpp
// 负责解析 Option 配置并生成运行态菜单结构；
// 不负责执行 Setting 保存和系统命令发送。
class OptionMenuConfigParser
{
};
```

Do not add a trivial comment to an obvious data structure.

Avoid:

```cpp
// 产品配置。
struct ProductConfig
{
};
```

## Function comments

Do not require comments for every public API.

Add a function comment only when at least one non-obvious semantic exists.

### Required triggers

#### Call order

```cpp
// 必须在 View 创建完成后调用，否则无法同步当前焦点状态。
void bind_view(OptionView& view);
```

#### Ownership or lifetime

```cpp
// 返回对象由 Registry 持有，调用方不得释放或跨配置重载长期缓存。
const RuntimeFact* find_fact(RuntimeFactId id) const;
```

#### Side effects

```cpp
// 更新本地状态后会同步发送系统命令。
bool save_setting(UiParam param, int value);
```

#### Return-value semantics

```cpp
// 返回 true 表示事件已消费，调用方不应继续向下分发。
bool handle_key_event(const KeyEvent& event);
```

#### Compatibility or fallback

```cpp
// 旧配置未提供 capability 时，回退到对应 domain 的默认 Gate。
bool is_feature_enabled(const FeatureGate& gate) const;
```

#### State transition

```cpp
// 保存成功后退出编辑态，但保留当前菜单层级。
void finish_editing();
```

#### Failure behavior

```cpp
// 配置缺失时保持当前主题，并返回 false。
bool apply_theme_profile(const std::string& profile_id);
```

#### Behavior contrary to the obvious reading of the name

```cpp
// 仅清理当前授权会话，不恢复已经写入的用户设置。
void clear_permission_session();
```

Do not add comments for ordinary:

- constructors;
- initialization;
- reset functions;
- getters and setters;
- simple forwarding functions;
- direct lookups;
- obvious predicates.

A comment must add information not already expressed by the function name and type.

## Internal comments

Add an internal comment when the code cannot clearly express why a step is necessary.

Typical triggers:

### Ordering constraint

```cpp
// 先清空旧焦点，避免重建列表时 FocusStack 持有失效对象。
clear_focus();

rebuild_items();
```

### Idempotency or repeated callbacks

```cpp
// 系统可能重复返回成功结果，这里必须保持幂等。
if (!m_pending_operation)
{
	return;
}
```

### Platform or environment fallback

```cpp
// PC 仿真环境没有打包流程，直接从产品资源根目录读取。
return resolve_from_product_root(relative_path);
```

### Intentional partial success

```cpp
// 本地状态立即生效；命令失败时保留当前值，等待后续状态同步修正。
m_current_value = value;
```

### Lifetime and asynchronous capture

```cpp
// 回调由任务队列延迟执行，node_id 必须按值捕获。
m_executor.post([this, node_id]()
{
	open_node_(node_id);
});
```

### Unsafe or non-obvious cast

```cpp
// 第三方接口错误地要求可写指针，实际不会修改该字符串。
char* mutable_text = const_cast<char*>(text);
```

Do not comment obvious operations.

Avoid:

```cpp
// 判断是否初始化
if (!m_initialized)
{
	return;
}

// 清空列表
m_items.clear();
```

Do not number ordinary business steps unless the function implements a genuinely strict multi-stage algorithm or protocol.

## Strong-enum comments

Every enumerator in a new or substantively modified `enum class` has a concise Chinese line-end comment.

```cpp
enum class RuntimeState
{
	Idle,		// 当前没有运行态任务
	Running,	// 正在处理运行态任务
	Suspended	// 临时暂停并保留当前上下文
};
```

Rules:

- use line-end comments, not pre-comments;
- explain intent or semantic difference;
- do not merely translate the enumerator name;
- keep the wording concise;
- align comments only inside one compact enum when alignment remains readable;
- prefer tabs for the alignment and use spaces only for minimal fine adjustment;
- abandon alignment when it would create excessive whitespace or broad diff churn;
- do not add a trailing comma to the final enumerator;
- review all enumerators when the enum is substantively modified.

Avoid:

```cpp
Idle  // 空闲
```

Prefer:

```cpp
Idle  // 当前没有运行态任务
```

## TODO and FIXME

Use `TODO` only for concrete technical debt. State what must be removed or changed and the condition that makes cleanup possible. Link a tracking issue when one exists.

```cpp
// TODO: 协议版本全部升级到 v3 后，删除旧字段兼容分支。跟踪：#184
```

Use `FIXME` only for a known defect and associate it with a tracking item.

```cpp
// FIXME: 重连期间可能重复注册回调。跟踪：#219
```

Do not introduce `HACK` or `XXX` as synonyms. Do not write vague notes such as `TODO: optimize later` or `TODO: temporary handling`.

Do not defer work required by the current request into a TODO without explicit user approval.

## Ownership and lifetime comments

Add a comment when ownership is not obvious from the type or interface.

Typical cases:

- a returned raw pointer is owned elsewhere;
- a returned object must not be cached;
- a callback may outlive its registering scope;
- shared ownership is necessary for a non-obvious reason;
- a `weak_ptr` is used to prevent a cycle;
- a function takes ownership despite a name that does not make it clear;
- an external framework controls destruction.

Do not repeat obvious semantics of `unique_ptr`, references, or a clearly named ownership-taking function.

## Error and result comments

Add a comment when:

- `true` means handled rather than successful;
- `false` means not applicable rather than failed;
- an empty result is normal;
- a failure intentionally preserves partial state;
- a return pointer has special lifetime restrictions;
- an error is deliberately ignored.

Example:

```cpp
// 刷新失败不影响当前保存结果，下一次参数同步会再次刷新。
(void)refresh_runtime_state();
```

Do not use a comment to justify ignoring an error when the surrounding behavior does not actually make that safe.

## Logs are not comments

Logs should contain runtime diagnostic context.

Comments should explain design intent and constraints.

Do not replace an important code comment with a log statement, and do not retain a comment that merely repeats an adjacent log.

## Low-value comments to avoid

Do not add comments that:

- restate a function or variable name;
- translate one obvious English identifier into Chinese;
- narrate direct assignments;
- describe the syntax of an `if`, loop, or return;
- duplicate README or design-document content without local relevance;
- repeat the same explanation in both header and source;
- create a comment wall around simple APIs;
- use decorative separators;
- preserve an outdated implementation description.

## Header versus source placement

Place externally relevant contract comments in the header when callers need them to use the API correctly.

Examples:

- ownership;
- return semantics;
- call order;
- failure behavior;
- thread or lifecycle constraints.

Place implementation-only reasoning in the source.

Examples:

- local ordering;
- fallback mechanics;
- platform workarounds;
- idempotency handling;
- why a specific branch exists.

Do not duplicate the same full explanation in both files.

## Comment review during modification

When a complete structural unit is substantively modified, review every comment inside or directly attached to that unit.

For each comment:

- keep it if still accurate and useful;
- update it if behavior or constraints changed;
- delete it if it became false or redundant;
- add a comment if the change introduced a non-obvious boundary.

Never modify an implementation while leaving a directly related comment that still describes the old behavior.

Do not batch-rewrite comments in unrelated structural units.

## Audit checklist

For every applicable change or review, check:

- Are multiple responsibilities clearly grouped?
- Does any class need a boundary comment?
- Does any API have non-obvious ownership, side effects, order, fallback, or return semantics?
- Does any internal step require a reason comment?
- Are there comments that merely restate code?
- Did the change invalidate an existing comment?
- Do all modified strong-enum items have concise Chinese line-end comments?
- Are new comments concise and free of decorative noise?
