## Description

The side-effect analysis for built-in constructor calls is not precise enough, leading to incorrect results when constructors are passed arguments that could trigger user-defined coercion code.

Currently, certain built-in constructors are treated as always side-effect-free regardless of what arguments they receive. This is wrong for constructors that coerce their arguments to a primitive type — if the argument is an unknown variable or an object with custom coercion methods, user code could be invoked as part of the coercion, which is a side effect. On the other hand, some constructors are being treated inconsistently even though they are genuinely safe with any argument.

## Expected Behavior

- Primitive wrapper constructors for strings and numbers should only be considered safe when their argument is a known primitive or a plain object with no custom coercion methods. An unknown variable or an object with a custom conversion method should be flagged as potentially having side effects.
- Date/time and buffer constructors follow similar rules: safe with no args or primitive literals, unsafe with unknown variables.
- Typed array constructors should be considered safe with numeric arguments or plain object literals, but unsafe with unknown variables or objects that define a custom iterator.
- The generic object wrapper and boolean wrapper constructors should always be considered safe, regardless of the argument, since they do not invoke user-defined coercion logic.
- Constructors for error objects should always be considered safe regardless of the argument passed.

## Why This Matters

Minifiers use side-effect analysis to determine which expressions can be eliminated during dead-code removal. Overly conservative analysis keeps code that could safely be removed; overly permissive analysis removes code that should be preserved. This fix ensures both directions are correct for a wide class of common constructor patterns.
