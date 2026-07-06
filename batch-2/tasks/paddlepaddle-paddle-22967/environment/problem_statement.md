## Description

The dynamic-to-static graph conversion tool does not correctly handle conditional expressions that combine tensor-based checks with regular Python comparisons through logical operators. When a condition mixes tensor operations and plain Python values using "and" or "or", the converter either fails or produces incorrect static graph code.

## Expected Behavior

- A visitor-based interface should be introduced to determine whether a given conditional expression requires static graph control flow. This interface should also expose a transformation step that rewrites compound conditions (with logical operators mixing tensor and Python subexpressions) into equivalent sequences of static graph logical operations.
- For simple conditions that are purely Python-level (arithmetic, None checks, etc.), the visitor must report no control flow is needed and perform no transformation.
- For simple tensor comparisons without logical operators, the visitor must report control flow is needed but apply no transformation.
- For compound conditions with logical operators that mix tensor operations and Python values, the visitor must report control flow is needed and produce a sequence of assignment nodes that represent the transformed logic.
- A separate, lower-level visitor must support checking whether a variable in an expression is a Paddle tensor based on an externally provided type map, and report True or False accordingly.
- The existing helper for collecting variable name identifiers from AST nodes must remain available.

## Why This Matters

Developers writing dynamic-graph models often use compound conditional expressions. Without this fix, any such model that mixes tensor checks with Python conditions cannot be converted to a static graph for deployment. This change enables those patterns to be handled correctly, broadening the range of dynamic models that can be exported to optimized static graph form.
