I'm working on improving a lint rule that detects return type annotations that are wider than what a function actually returns.

*   The lint rule that detects misleadingly wide return type annotations must flag union return types where one or more union variants are never actually returned by the function body. This applies to regular functions, async functions, arrow functions, class methods, class getters, and object literal methods.

*   When a function's return type annotation is a union (e.g. 'T | null', 'T | U', 'T | U | null') and the function only ever returns values of a strict subset of those variants, the rule must emit the diagnostic message 'The return type annotation is wider than what the function actually returns.' along with the hint 'A wider return type hides the precise types that callers could rely on.'

*   When the rule can determine a concrete narrower type suggestion (a single type or a union of the actually-returned variants), it must emit the note 'Consider using X as the return type.' where X is the narrowed type. When a specific suggestion cannot be determined, it must emit the note 'Narrow the return type to match what the function actually returns.'

*   For async functions whose return type annotation is 'Promise<T | null>' (or any Promise-wrapped union), the rule must apply the same widening check to the inner type and produce the same diagnostics.

*   When a function returns a value using an 'as const' type assertion, the rule must infer the precise literal type from the assertion and use it when determining whether the annotation is too wide and what narrower type to suggest.

*   When the return type annotation is a type alias that resolves to a union type, the rule must resolve the alias and apply the same widening check as for inline union annotations.

*   For multi-branch functions (e.g. using conditional branches), the rule must analyze all reachable return paths. Branches that throw an exception do not count as returning a value. The set of actually-returned types is the union of all return-expression types across all non-throwing branches.

*   The rule must NOT flag union return types that contain 'any' or 'unknown' as a variant, because these special types absorb all other variants and no narrowing is meaningful.

*   The rule must NOT flag union return types that contain 'never' as a variant.

*   The rule must NOT flag union return types where every literal type in the union is absorbed (subsumed) by a corresponding primitive type also present in the union (e.g. '"a" | string', '"a" | "b" | string', '1 | 2 | number', '1n | bigint'). These collapse to a single primitive and no narrowing is needed.

*   The rule must NOT flag union return types where all variants of the union are actually covered by return statements across different execution paths.

*   When suggesting a narrowed type from the annotation's union, the suggestion must only include the variants of the annotation that are actually covered by at least one return statement. The suggested type must be at most 80 characters long; if it would exceed this length, the rule must fall back to the generic 'Narrow the return type to match what the function actually returns.' note.

*   Nested union types (e.g. 'T | (U | null)') must be flattened and treated the same as flat unions for the purposes of the widening check.

*   An existing case where a function's return type annotation included 'boolean' but the annotation was wider must now produce the note 'Consider using boolean as the return type.' rather than the generic narrowing note.


*   Interface details: NO INTERFACES NEEDED

The tests are snapshot-based spec tests that run the `noMisleadingReturnType` lint rule end-to-end against TypeScript fixture files and compare the diagnostic output to stored snapshots. The tests do not import or call specific Rust functions by name — they rely entirely on the rule's observable output (diagnostic messages, source positions, and hint text).

The implementation must be modified in:
  `crates/biome_js_analyze/src/lint/nursery/no_misleading_return_type.rs`

The test fixtures and snapshots are located at:
  `crates/biome_js_analyze/tests/specs/nursery/noMisleadingReturnType/invalid.ts`
  `crates/biome_js_analyze/tests/specs/nursery/noMisleadingReturnType/invalid.ts.snap`
  `crates/biome_js_analyze/tests/specs/nursery/noMisleadingReturnType/valid.ts`
  `crates/biome_js_analyze/tests/specs/nursery/noMisleadingReturnType/valid.ts.snap`

The exact diagnostic strings the snapshots require are:
  - Main message: "The return type annotation is wider than what the function actually returns."
  - Hint: "A wider return type hides the precise types that callers could rely on."
  - Specific suggestion note: "Consider using <type> as the return type."
  - Generic fallback note: "Narrow the return type to match what the function actually returns."


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.