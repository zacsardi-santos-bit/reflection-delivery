I'd like to add a new lint rule to Biome's nursery group that detects a common TypeScript anti-pattern: using type assertions on the initial value of array reduction calls instead of passing the type as a type parameter on the call itself.

*   The rule must detect calls to `reduce` and `reduceRight` on arrays (and tuple arrays) where the initial value uses a TypeScript type assertion (`as Type` or `<Type>expr` syntax, including parenthesized forms), and emit a diagnostic for each such call.

*   For `reduce` calls, the primary diagnostic message must be: "The initial value of Array#reduce uses a type assertion." For `reduceRight` calls, it must be: "The initial value of Array#reduceRight uses a type assertion."

*   The secondary (informational) diagnostic message must be: "Type assertions can hide type mismatches in the reducer callback. Use a type parameter on the call instead, so TypeScript checks the return type."

*   The rule must provide an unsafe fix. When the `reduce`/`reduceRight` call has no existing type parameter, the fix message must be "Use a type parameter instead of type assertion." and the fix must move the asserted type to a type parameter on the call and remove the assertion from the initial value (e.g., `arr.reduce(f, [] as number[])` becomes `arr.reduce<number[]>(f, [])`).

*   When the `reduce`/`reduceRight` call already has a type parameter, the fix message must be "Remove the type assertion from the initial value." and the fix must only remove the type assertion from the initial value, leaving the existing type parameter unchanged.

*   The rule must handle all of these assertion forms on the initial value: `as Type`, `<Type>expr` (angle-bracket), `(expr as Type)` (parenthesized as), and `(<Type>expr)` (parenthesized angle-bracket).

*   The rule must handle complex types in assertions: array types, object types, generic types (e.g., Record<K,V>), tuple types (e.g., [string, number][]), union types (e.g., string | number), and intersection types (e.g., Foo & Bar).

*   The rule must NOT emit a diagnostic when: the `reduce`/`reduceRight` call has no initial value; the initial value has no type assertion; the call already uses a type parameter and the initial value has no type assertion; the initial value uses `satisfies` instead of `as`; or the method called is not `reduce` or `reduceRight`.

*   The rule must be registered under the `nursery` rule group with the identifier `useReduceTypeParameter`.

*   The rule source file must be at `crates/biome_js_analyze/src/lint/nursery/use_reduce_type_parameter.rs` and the rule must be registered in the nursery module.


*   Interface details: Type: Struct (Lint Rule)
Name: UseReduceTypeParameter
Location: crates/biome_js_analyze/src/lint/nursery/use_reduce_type_parameter.rs
Description: A lint rule in the `nursery` group that detects `reduce` and `reduceRight` calls where the initial value uses a type assertion, and offers an unsafe fix to use a type parameter instead. Must be declared using the `declare_lint_rule!` macro with name "useReduceTypeParameter", language "ts", and `fix_kind: FixKind::Unsafe`.

Rule identifier: `useReduceTypeParameter`
Rule group: `nursery`
Diagnostic category: `lint/nursery/useReduceTypeParameter`

Diagnostic messages (exact strings required by snapshot tests):
- Primary message for `reduce`: "The initial value of Array#reduce uses a type assertion."
- Primary message for `reduceRight`: "The initial value of Array#reduceRight uses a type assertion."
- Secondary (note) message: "Type assertions can hide type mismatches in the reducer callback. Use a type parameter on the call instead, so TypeScript checks the return type."

Fix labels (exact strings required by snapshot tests):
- When no existing type parameter: "Use a type parameter instead of type assertion."
- When existing type parameter present: "Remove the type assertion from the initial value."

Fix kind: Unsafe (both fixes are labeled as "Unsafe fix")

---

Type: Struct (Rule Options)
Name: UseReduceTypeParameterOptions
Location: crates/biome_rule_options/src/use_reduce_type_parameter.rs
Description: Empty options struct for the UseReduceTypeParameter rule. Must derive Default, Clone, Debug, Deserialize, Deserializable, Merge, Eq, PartialEq, Serialize and have `#[serde(rename_all = "camelCase", deny_unknown_fields, default)]`.

---

Additional files that must be modified for the rule to compile and be recognized:

1. crates/biome_rule_options/src/lib.rs
   - Add: `pub mod use_reduce_type_parameter;`

2. crates/biome_diagnostics_categories/src/categories.rs
   - Add the entry: `"lint/nursery/useReduceTypeParameter": "https://biomejs.dev/linter/rules/use-reduce-type-parameter",`
   - (Insert in alphabetical order within the nursery section)

3. crates/biome_configuration/src/analyzer/linter/rules.rs
   - Add `UseReduceTypeParameter` to the `RuleName` enum (alphabetical order)
   - Add its string representation `"useReduceTypeParameter"` in the `Display`/`to_str` impl
   - Add its group mapping to `RuleGroup::Nursery` in the group mapping impl
   - Add its `FromStr` parse arm `"useReduceTypeParameter" => Ok(Self::UseReduceTypeParameter)`

4. crates/biome_configuration/src/generated/linter_options_check.rs
   - Add an entry for `("nursery", "useReduceTypeParameter", TypeId::of::<biome_rule_options::use_reduce_type_parameter::UseReduceTypeParameterOptions>())`


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.