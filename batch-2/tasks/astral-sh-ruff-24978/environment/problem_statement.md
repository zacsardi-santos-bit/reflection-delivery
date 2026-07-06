## Description

The language server's code folding feature does not correctly handle Python block headers that span multiple lines. When a function signature has its parameters broken across several lines, or when a structural pattern match case has a complex multi-line pattern, the folding ranges reported to editors are incomplete or incorrect.

## Expected Behavior

- When a function's parameter list spans multiple lines (each parameter on its own line inside the parentheses), users should be able to fold just the parameter list independently.
- The body fold for such a function should start from the end of the closing line of the header — not from the beginning of the function keyword — so that the full header remains visible when the body is collapsed.
- When a match case uses a pattern that spans multiple lines (e.g., a dictionary or sequence pattern with entries on separate lines), users should be able to fold just the pattern content.
- Similarly, the case body fold should start from the end of the multiline pattern's closing line, keeping the full case header visible when the body is folded.
- Multiline delimiter content that is already represented as a block header fold should not also appear as a redundant standalone expression fold.

## Why This Matters

Developers working with complex Python code — such as functions with many typed parameters or match statements with detailed patterns — rely on code folding to manage visual complexity. Without correct folding for multiline headers, editors either show no fold region for the header, or show a fold region that hides part of the header when collapsing the body. This makes it harder to navigate and read large Python files.
