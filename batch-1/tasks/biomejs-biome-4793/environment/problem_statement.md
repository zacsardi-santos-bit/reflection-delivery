## Description

The Biome CSS parser and linter do not support two modern CSS at-rules that are part of current web specifications: one for configuring anchor-based positioning fallbacks and one for controlling animated view transitions between pages. When CSS files contain these at-rules, the parser fails to build a proper syntax tree for them, and the linter incorrectly flags them as unknown or unrecognized constructs.

This prevents developers from using these modern CSS features in projects that use Biome for linting and formatting, since they will always receive false-positive warnings.

## Expected Behavior

- The CSS parser should correctly parse and build a structured syntax tree for the anchor-positioning at-rule (which takes a dashed custom identifier name and a declaration block) and for the view-transition at-rule (which takes only a declaration block).
- The linter's unknown at-rule check should recognize both at-rules as valid, well-known CSS constructs and not flag them as errors.
- Both at-rules should support empty blocks as well as blocks containing standard CSS property declarations.

## Why This Matters

Modern web developers are increasingly relying on these CSS features in production. As long as Biome cannot parse or recognize them, it generates false lint errors and may corrupt or reject stylesheets that use them. Adding support removes this friction and keeps Biome compatible with the evolving CSS standard.
