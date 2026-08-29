---
name: cpp-code-quality
description: Apply this Skill whenever a task creates, modifies, refactors, formats, or performs a focused code-quality review of C++17 headers or source files. Enforce complete-structural-unit formatting, responsibility grouping, comments, naming, basic readability and ownership boundaries, legacy-code scope control, and non-destructive validation. Do not trigger it merely because a read-only investigation touches C++ files.
---

# C++ Code Quality

## Purpose

Use this Skill as the mandatory C++ code-quality baseline.

Target C++17 unless the current user request or an explicitly authorized repository rule requires another language version.

It governs:

- formatting and layout;
- header and source responsibility grouping;
- comments and documentation;
- basic naming;
- modification scope;
- legacy-code handling;
- non-destructive verification.

It does not decide architecture ownership, module decomposition, API consolidation, namespace design, or whether a class should be a Manager, Service, Facade, Registry, Resolver, Builder, Request, Spec, or Context. Those decisions belong to a separate architecture/refactoring Skill.

## Mandatory rule loading

For every applicable task:

1. Read this `SKILL.md`.
2. Always read `formatting-and-layout.md`.
3. Always read `comments-and-documentation.md`.
4. Check the repository root and its top-level `docs/` directory for project-specific C++ rules.
5. Merge the active rules according to the priority defined below.
6. Determine the task mode.
7. Apply both rule files before editing or reviewing code.
8. Complete the final self-check before responding.

Neither rule file is optional or read-on-demand.

Do not complete a C++ task after applying only formatting rules or only comment rules.

## Applicability

Use this Skill whenever a task creates, modifies, refactors, formats, or performs a focused code-quality review of:

- `.h`
- `.hpp`
- `.hh`
- `.cpp`
- `.cc`
- `.cxx`

Also use it for explicit read-only audits of C++ formatting, grouping, naming, and comments.

Do not use it merely for call-chain tracing, impact analysis, repository fact-finding,
or other top-level read-only investigation where no C++ quality judgment is yet required.

When a C++ task also requires adding or updating Visual Studio project entries, modify only the necessary project files. Apply the Visual Studio encoding and generated-layout exceptions from `formatting-and-layout.md`; do not apply C++ layout rules to XML or solution files.

Do not automatically apply C++-specific class, constructor, member, or namespace rules to pure C files or pure C headers. For mixed C/C++ headers, apply only the rules relevant to the actual constructs being changed.

The following are excluded by default:

- third-party code;
- vendored code;
- generated code;
- build output;
- copied upstream dependencies.

Typical excluded paths include:

- `third_party/`
- `external/`
- `vendor/`
- `generated/`
- `build/`

Modify excluded code only when the user explicitly requires it. Keep the change minimal and preserve the upstream component's own style unless the user explicitly requests otherwise.

## Rule priority

Apply rules in this order:

1. Explicit requirements in the current user request.
2. Explicitly authorized repository-level C++ rules.
3. `formatting-and-layout.md`.
4. `comments-and-documentation.md`.

Legacy code is never a formatting or comment-style authority.

Use existing code only to understand:

- behavior;
- compatibility;
- call relationships;
- domain terminology;
- ownership already established by the design;
- interfaces that must remain compatible.

Do not infer indentation, braces, blank lines, wrapping, grouping, comment language, or naming rules from nearby legacy code.

## Project-specific rule discovery

Check only the repository root and its top-level `docs/` directory for these files:

1. `docs/coding-standards.md`
2. `CODING_STYLE.md`
3. `CODE_STYLE.md`
4. `docs/code-style.md`
5. `docs/cpp-style.md`
6. `.clang-format`
7. `CONTRIBUTING.md`

Do not recursively search the whole repository for style documents.

Ignore style files under third-party, vendored, generated, build, dependency, or nested submodule directories unless the current task explicitly targets that component.

Use only rules that explicitly apply to C++.

### Project formatting override policy

Project files and tools must not silently override this Skill.

The presence of any of the following is not sufficient authorization to override the global baseline:

- `.clang-format`;
- a formatting script;
- a CI formatting check;
- a build command that invokes a formatter;
- nearby legacy formatting.

Project mechanical formatting rules may override this Skill only when:

- the current user request explicitly requires the project format; or
- a repository-level coding standard explicitly states that a named formatter or configuration is authoritative.

Do not ask the user for confirmation on every task.

When a conflict exists without explicit override authorization:

- continue using this Skill's baseline;
- do not run the conflicting formatter;
- briefly report the detected conflict in the final result.

When explicit authorization exists:

- apply the authorized mechanical rules without asking again;
- report the mechanical rules that actually affected the current task;
- continue applying this Skill's scope, grouping, comment, naming, legacy-code, and validation rules.

An authorized formatter may override only mechanical rules it can express, such as:

- indentation;
- brace placement;
- line wrapping;
- pointer and reference binding;
- initializer-list layout;
- `case` indentation;
- include sorting.

It cannot replace this Skill's rules for:

- complete structural-unit normalization;
- modification scope;
- responsibility grouping;
- comments;
- comment language;
- legacy-code handling;
- naming;
- third-party and generated-code boundaries.

Never run whole-file formatting automatically on a legacy file unless the user explicitly requests whole-file formatting. If a formatter changes unrelated code, revert the unrelated changes.

## Task modes

Infer the mode directly from the user's request. Do not ask the user to select a mode unless the request is genuinely contradictory.

### Implementation or refactoring

Use for feature work, bug fixes, behavior changes, interface changes, and refactoring.

Requirements:

- complete the requested behavior change;
- normalize every substantively modified structural unit;
- review related grouping and comments;
- do not reformat unrelated code.

### Readability cleanup

Use when the user explicitly requests formatting, grouping, comment cleanup, or readability improvement.

Requirements:

- preserve behavior;
- work only within the user-specified file, class, or module boundary;
- allow responsibility grouping and function reordering within that boundary when needed;
- do not expand into unrelated modules.

### Audit or review

Use for explicit read-only style, formatting, comment, and code-quality audits.

Requirements:

- do not modify files by default;
- identify concrete problems and their impact;
- provide actionable corrections;
- prioritize findings when useful;
- switch to an editing mode only when the user explicitly requests changes.

Do not enter this mode merely because the current task opened C++ files while
investigating architecture, call chains, or impact boundaries.

## Modification scope

Use the complete structural unit as the minimum normalization boundary.

A structural unit includes a complete:

- function declaration;
- function definition;
- constructor initializer list;
- control-flow statement;
- `switch`;
- class access section;
- coherent responsibility group;
- struct;
- enum;
- macro;
- namespace block directly involved in the change.

### Required scope

Always normalize every structural unit that is substantively modified.

Examples:

- modifying a function body requires normalizing the complete function;
- modifying a declaration requires normalizing the complete declaration and its corresponding definition when both are part of the change;
- modifying one `case` requires normalizing the complete `switch`;
- modifying an initializer requires normalizing the complete initializer list;
- modifying an enum requires reviewing the complete enum.

Never leave one modified structural unit partially in legacy style and partially in the active style.

### Allowed adjacent scope

You may normalize or reorder directly related code within the same class and responsibility group when required to:

- keep declarations and definitions aligned;
- place new functions or members in the correct responsibility group;
- avoid mixed styles inside one coherent section;
- update comments made inaccurate by the change.

Code is directly related only when at least one of these applies:

- it belongs to the same class and responsibility group;
- it is the matching declaration or definition;
- it is a member directly used by the changed interface;
- it must move to preserve header/source responsibility order;
- leaving it unchanged would create a visibly mixed coherent section.

Being in the same file is not sufficient reason to expand the change.

### Prohibited expansion

Do not reformat, reorder, rename, or rewrite unrelated:

- functions;
- classes;
- includes;
- comments;
- files;
- modules.

Do not perform whole-file formatting unless explicitly requested.

Do not rename unrelated legacy identifiers merely to improve consistency.

## Legacy-code handling

Existing code is a behavioral and compatibility reference, not a style source.

Do not copy legacy formatting or low-value comment patterns into new code.

When a legacy structural unit is substantively modified, normalize that complete unit to the active rules.

Do not normalize unrelated legacy units solely for global consistency.

Do not let legacy code decide:

- tabs versus spaces;
- brace style;
- blank-line policy;
- parameter wrapping;
- declaration grouping;
- definition grouping;
- comment language;
- namespace formatting;
- naming of new identifiers.

## Execution workflow

For an applicable task:

1. Identify the repository and target files.
2. Load both mandatory rule files.
3. Check the limited project-rule candidates.
4. Resolve explicitly authorized overrides.
5. Determine whether the target is project code, pure C, third-party code, or generated code.
6. Determine the task mode.
7. Define the smallest complete structural-unit boundary.
8. Perform the change or review.
9. Check header/source grouping and comment accuracy.
10. Inspect the diff for unintended expansion.
11. Run non-destructive validation when available.
12. Report only actual changes, important boundaries, validation results, and unresolved issues.

Do not tell the user that rule files were loaded unless a rule conflict materially affected the result.

## Required self-check

After modifying C++ code, verify all applicable items.

### Scope

- Every substantively modified structural unit is fully normalized.
- No modified unit mixes legacy and active styles.
- No unrelated code was reformatted or reordered.
- Matching declarations and definitions remain consistent.

### Formatting

- Block indentation follows the active rule.
- Braces follow the active rule.
- Every `case` follows the required independent-scope layout.
- Constructor initializer lists follow the active layout and declaration order.
- Function calls, expressions, and signatures were not split without readability benefit.
- In Windows-targeted code, standard `min` and `max` calls use explicit template arguments when Windows headers may define the corresponding macros.
- Blank-line rules are consistent.
- No trailing whitespace or whitespace-only blank lines remain.

### Structure

- Header declarations are grouped by responsibility.
- Private functions and private data members use separate `private:` sections.
- Source definitions follow the header's responsibility order when the current change affects that structure.
- Multi-responsibility source sections have appropriate group comments.

### Comments

- Non-obvious ownership, lifetime, side effects, call order, fallback, compatibility, state transition, and return semantics are documented.
- Comments explain intent, constraints, or reasons rather than restating code.
- Existing comments affected by the change remain accurate.
- New comments use the active language rule.
- Modified strong enums have concise line-end comments for every enumerator.

### Naming

- New identifiers follow the active naming rules.
- New code does not copy obviously inconsistent legacy naming.
- No unrelated legacy identifiers were renamed.

## Validation

Use non-destructive checks by default.

In a Git repository, run when practical:

```bash
git diff --check
```

Also use lightweight, non-build checks that directly fit the task, such as focused text searches, diff inspection, encoding checks, or metadata validation.

Do not automatically run builds, compilation, linking, tests, MSBuild, runtime verification, or commands that create broad generated output merely to finish a code-quality task. Run them only when the current user request explicitly asks for that validation or an authorized repository workflow requires it and the user has not prohibited it.

Do not add a custom checking script as part of this Skill.

Do not claim that a command passed unless it was actually run.

If validation could not be run, state that explicitly.

## Final response

Do not use a fixed template merely to announce the task mode or list loaded rules.

For implementation or cleanup work, report:

- the meaningful changes;
- important scope or behavior boundaries;
- validation performed and its result;
- anything not completed or not verified.

For an audit, report:

- concrete findings;
- impact;
- recommended correction;
- priority when useful.
