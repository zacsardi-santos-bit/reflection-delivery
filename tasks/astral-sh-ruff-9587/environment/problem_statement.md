## Description

The lint rule that flags unnecessary direct calls to special dunder methods (instead of using their equivalent operator syntax) is missing violations in many common expression contexts. Even when a dunder operator method call is clearly unnecessary, the linter silently ignores it if it appears under a unary operator, inside a lambda, in a conditional expression, in a container literal, in a comprehension, in a generator expression, or in a subscript/starred/slice context.

Additionally, when the rule does detect a violation and suggests an automatic fix, the fix can produce semantically incorrect code. If the replacement operator expression appears in a position where operator precedence matters — such as when it is the operand of a multiplication or sits directly under a negation — the fix omits parentheses that are required to preserve the original meaning. This means applying the suggested fix can silently change program behavior.

## Expected Behavior

- The rule should flag violations wherever dunder operator methods are called unnecessarily, regardless of the surrounding expression context: unary operators on the receiver, lambda bodies, conditional expressions, container literals (dict, set, list, tuple), comprehensions of all kinds, generator expressions, subscript values, starred expressions, and slice elements
- When a dunder is flagged but has no simple operator equivalent, the rule must still report it without offering a fix
- Auto-fixes that swap a dunder call for an operator must be marked as unsafe
- Auto-fixes must add parentheses around the replacement expression when the surrounding context requires them for correct precedence
- Auto-fixes must add parentheses around complex arguments (such as binary expressions) to preserve semantics

## Why This Matters

Developers writing idiomatic Python code in any of these common patterns will receive no linting guidance, even though the code could be simplified. And even for the cases that are detected, a user who applies the auto-fix risks silently introducing a bug if parentheses are omitted.
