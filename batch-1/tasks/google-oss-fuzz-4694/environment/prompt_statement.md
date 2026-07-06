I'm working on a compiler wrapper script used in sanitizer library builds. There's a function that's supposed to strip certain linker flags that enforce strict symbol resolution — flags that cause linker failures when symbols are intentionally left undefined during sanitizer instrumentation.

The function needs to be renamed to follow standard Python conventions (lowercase with underscores instead of camel case). Beyond the rename, the function's logic has some gaps I need to fix:

First, the standalone form of the "no undefined symbols" linker option isn't being stripped — only the version embedded in a compound flag group is handled. Both forms should be removed.

Second, when the flag is spread across two separate linker tokens and there's an unrelated argument between them, the function fails to detect and remove the pair. It should handle this split case even with intervening arguments.

The function should otherwise continue to preserve all other linker flags, including flags that control memory layout and related options that aren't about symbol-definition enforcement.
