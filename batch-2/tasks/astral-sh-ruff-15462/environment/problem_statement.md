## Description

The linter's suggested autofix for non-annotated FastAPI dependency parameters produces incorrect code when those parameters specify a default value inside the dependency call. Instead of moving the default value outside to become the parameter's actual default, the old fix embeds it inside the type annotation, which is semantically wrong and can break Python's ordering rules for function parameters.

## Expected Behavior

- When a handler parameter uses a dependency type call with a positional default (e.g. the first argument to the call is the default value), the autofix should extract that default and use it as the standalone parameter default, leaving the dependency call empty or containing only the non-default keyword arguments.
- When the dependency call uses a named keyword argument specifying the default value instead of a positional default, the autofix should handle it identically — removing that keyword from the call and placing the value as the standalone parameter default.
- When there are additional keyword arguments alongside the default, they must be preserved inside the dependency call while the default is moved out.
- The fix must produce valid Python in all cases, including when the fixed parameter comes after other parameters that already have defaults.

## Why This Matters

The previous behavior generated code that was at best unnecessarily verbose (keeping defaults in the annotation) and at worst syntactically invalid (violating Python's rule that non-default parameters cannot follow default parameters). Developers relying on the autofix received broken suggestions, defeating the purpose of the automated fix.

A new test fixture has been added to cover these three scenarios: a positional default only, a keyword default only, and a positional default combined with additional keyword arguments.
