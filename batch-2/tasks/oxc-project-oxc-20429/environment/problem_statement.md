## Description

The variable shadowing lint rule is producing false positive warnings when the "check built-in globals" option is enabled. Specifically, the rule flags local variable declarations as shadowing built-in globals even when those variable names do not actually belong to any of the runtime environments the user has configured. For example, declaring a local variable inside a function causes a spurious warning claiming it shadows a global, even though that name is not a recognized global in the configured environment.

## Expected Behavior

- When checking for shadowing of built-in globals, the rule should only consider globals that belong to the explicitly enabled runtime environments (e.g., browser, node).
- Variable names that are not present in any configured environment's global list should NOT produce a warning, even when the "check built-in globals" option is enabled.
- Variables that genuinely shadow a global from a configured environment should still be flagged correctly.
- Checking both browser and node environments simultaneously should flag only variables that are actual globals of those environments, not unrelated names.

## Why This Matters

Developers rely on the shadowing lint rule to catch real naming conflicts, not false alarms. When the rule warns about variables that aren't actually globals in the target environment, it creates noise that erodes trust in the linter. Teams may start suppressing or ignoring these warnings, which risks missing real shadowing issues. The rule should be environment-aware and only flag what is actually relevant to the project's configured runtime.
