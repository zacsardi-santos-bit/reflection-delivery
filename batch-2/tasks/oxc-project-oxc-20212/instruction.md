I'm working on improving the side-effect analysis in a JavaScript minifier.

*   Accessing well-known built-in JavaScript global identifiers (Math, Array, Object, JSON, Reflect, Symbol, Promise, Map, Set, WeakMap, WeakSet, parseInt, parseFloat, isNaN, isFinite, encodeURI, decodeURI, globalThis) must be considered side-effect-free without any additional configuration.

*   When a list of explicit global variable names is provided, accessing globals from that list that are recognized as known-safe environment globals (such as console, document, window, fetch) must be considered side-effect-free. Accessing globals from the explicit list that are not in any known-safe set must still be considered as having side effects.

*   Reading known properties of built-in global objects must be considered side-effect-free: for Math — PI, E, abs, floor, random; for Object — keys, create, assign, prototype; for Reflect — apply, get; for Symbol — iterator, asyncIterator; for JSON — parse, stringify. Accessing any unlisted/unknown property on these objects must be considered as having side effects.

*   When the console object is included in the explicit global variables list, reading its known methods (log, error, warn) must be considered side-effect-free. Accessing unknown methods on console must be considered as having side effects.

*   Three-level property access chains on Object.prototype must be side-effect-free for the following properties: hasOwnProperty, isPrototypeOf, toString, valueOf, propertyIsEnumerable. Accessing Object.prototype.unknownProp must have side effects. Three-level chains starting from other globals (e.g., Math.PI.toString, Array.prototype.push) must NOT be treated as side-effect-free.

*   The built-in Symbol constructor called with no arguments must be treated as a pure/side-effect-free expression, allowing it to be inlined into its single usage site.

*   The arguments copy loop optimization (converting a for-loop that copies arguments into a spread) must also eliminate the intermediate array variable when it has a single use, inlining the spread expression directly into the usage.

*   When the keep_names option is active and an anonymous function expression is assigned to a variable that is then aliased, inlining must substitute the original named variable reference rather than merging declarations. For example, the pattern with two declarations (original and alias) should result in keeping the original declaration and replacing alias accesses with the original variable name.

*   When the keep_names option is active and a named function or named class expression is assigned and then aliased, the expression must be fully inlined at the usage site (no intermediate declarations preserved).

*   An if-else pattern where both branches call the same function but with different arguments — where one branch passes a condition value and the other a transformed value — must be reduced to a single call using the logical OR operator to select the argument.


*   Interface details: Type: Function
Name: test_with_global_variables
Location: crates/oxc_minifier/tests/ecmascript/may_have_side_effects.rs
Signature: test_with_global_variables(source: &str, globals: &[&str], expected: bool)
Description: Test helper used in the side-effect analysis tests. Parses and analyzes the given JavaScript source expression for side effects, using the provided list of identifiers as explicitly known global variable names. Checks whether the side-effect analysis result matches the expected boolean (true = has side effects, false = side-effect-free). This function is called in existing tests (closure_compiler_tests) and all three new test functions (test_known_global_identifiers, test_known_global_property_reads, test_known_global_property_deep).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.