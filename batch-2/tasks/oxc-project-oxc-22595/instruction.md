I'm working with the JavaScript minifier and I've noticed that pure-flag annotations (the markers used to indicate a constructor call has no side effects) aren't always being applied correctly.

*   When a constructor call's arguments contain string literals that can be concatenated (folded) during the peephole optimization loop, the constructor must be re-evaluated for pure/no-side-effects status after the fold. If the constructor is a known pure global (e.g., RegExp), it must receive a `/* @__PURE__ */` annotation in the output.

*   When a single-use variable holding a known-pure constructor call is inlined into another constructor's argument list during the peephole optimization loop, the outer constructor must be re-evaluated for pure status after the inline. Both the inlined inner constructor and the outer constructor must each receive a `/* @__PURE__ */` annotation if they qualify.

*   Pure-flag re-evaluation must happen on every iteration of the peephole loop, not only during the initial normalization pass. The optimization result must be idempotent: applying the same optimization pass a second time must produce identical output.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.