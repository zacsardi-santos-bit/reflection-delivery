## Description

The internal representation of a function call expression stores the arguments before the function being called (the callee). This means when the semantic analyzer traverses a call expression, it visits — and assigns identifiers to — the arguments *before* the callee. This ordering is logically backward: in JavaScript/TypeScript evaluation semantics, the callee is resolved before its arguments.

The incorrect field ordering propagates into the semantic analysis output: reference identifiers and AST node positions are assigned in argument-first order instead of callee-first order. This produces snapshot output where argument references have lower IDs than callee references, which is the reverse of the expected evaluation order.

## Expected Behavior

- In a call expression, the callee should be visited and analyzed before the arguments.
- The callee's reference identifiers and node positions should be numerically lower than those of the arguments, reflecting proper visitation order.
- All code that constructs or manipulates a call expression node must be updated to use the corrected field ordering.

## Why This Matters

Semantic analysis tools and downstream consumers that rely on reference ordering or node positions will receive correctly ordered data. The current incorrect ordering can cause confusion for any tool that assumes evaluation-order traversal of call expressions.
