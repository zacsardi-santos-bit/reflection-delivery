## Description

There is a bug in the template engine where including a template that uses template inheritance (i.e., a template that extends a base and overrides blocks) inside a loop does not work correctly after the first iteration.

## Expected Behavior

- When a child template extends a base template and overrides a block to display a loop variable, and that child template is included inside a for loop, each iteration should independently render the block with the current iteration's variable value.
- For example, a loop over three items including an extended template should produce each item's value in the output, one per iteration.

## Actual Behavior

Only the first loop iteration appears to produce output (or the rendering fails entirely on the second iteration). Subsequent iterations do not correctly render because the engine's cycle detection incorrectly identifies the repeated inclusion as a circular reference, even though no actual cycle exists.

## Why This Matters

Template inheritance and template inclusion are both fundamental features of this template engine. Composing them together — particularly including an inherited template inside a loop — is a natural and useful pattern. This bug makes it impossible to use such a composition correctly, forcing workarounds or preventing users from leveraging the full power of template inheritance within loops.
