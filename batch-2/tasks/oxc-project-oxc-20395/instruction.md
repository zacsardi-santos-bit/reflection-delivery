I'm working on improving the accuracy of side-effect analysis for built-in constructor calls in a JavaScript minifier.

*   When a string wrapper constructor is called with no arguments or with a known primitive argument (string, number, boolean, or null literal), it must be considered side-effect-free. When called with an unknown variable reference, it must be considered to have side effects, since the argument could be an object that invokes user-defined coercion code. When called with a plain empty object literal (no custom conversion methods), it must be considered side-effect-free. When called with an object literal that defines a custom string conversion method, it must be considered to have side effects.

*   When a numeric wrapper constructor is called with no arguments or with a known primitive argument (number, string, boolean, or null literal), it must be considered side-effect-free. When called with an unknown variable reference, it must be considered to have side effects. When called with a plain empty object literal, it must be considered side-effect-free. When called with an object literal that defines a custom numeric conversion method, it must be considered to have side effects.

*   When a date constructor is called with no arguments, a numeric literal, or a string literal, it must be considered side-effect-free. When called with an unknown variable reference, it must be considered to have side effects.

*   When a buffer constructor is called with no arguments or a numeric literal, it must be considered side-effect-free. When called with an unknown variable reference, it must be considered to have side effects.

*   Typed array constructors (Int8Array, Uint8Array, Uint8ClampedArray, Int16Array, Uint16Array, Int32Array, Uint32Array, Float32Array, Float64Array, BigInt64Array, BigUint64Array) called with a numeric literal argument must be considered side-effect-free. Called with an unknown variable reference, they must be considered to have side effects. Called with a plain empty object literal, they must be considered side-effect-free. Called with an object literal that defines a custom iterator, they must be considered to have side effects.

*   The generic object wrapper constructor must always be considered side-effect-free regardless of the argument passed — this includes unknown variable references and object literals.

*   The boolean wrapper constructor must always be considered side-effect-free regardless of the argument passed — this includes unknown variable references and object literals.

*   Error constructors (Error, TypeError, EvalError, RangeError, ReferenceError, SyntaxError, URIError) must always be considered side-effect-free regardless of the argument passed, including unknown variable references.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.