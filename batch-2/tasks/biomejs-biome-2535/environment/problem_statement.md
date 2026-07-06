## Description

CSS files sometimes contain measurement values with unit suffixes that are not recognized by browsers. This can happen due to typos (e.g., writing "remm" instead of "rem", or "pix" instead of "px"), or simply using made-up unit names. Browsers silently ignore unknown units, meaning broken layouts and visual errors can slip into production unnoticed.

There is currently no lint rule in the CSS analyzer to catch these mistakes. We need a new lint rule that flags any numeric dimension value whose unit is not a recognized CSS unit.

## Expected Behavior

- The rule should flag unknown units regardless of where they appear: property values, function arguments (including color functions, calculations, vendor-prefixed functions, and other functions), media query features, and CSS custom property values.
- The rule should be case-insensitive when checking against known units — valid units should be accepted regardless of letter casing, but unknown units written in any casing should still be flagged.
- Numbers in scientific notation followed by unknown units should be flagged.
- The single-letter resolution unit "x" is a special case: it is only valid in resolution-related contexts (image-set functions, the image-resolution property, and resolution media features). Using it anywhere else should be flagged.
- The rule should NOT flag units that appear inside comments, quoted strings, URL references, CSS variable references, preprocessor variable references, selector or property names — only actual CSS dimension values should be checked.

## Why This Matters

Typos in CSS units silently produce broken styles. Automating detection of unknown units helps developers catch mistakes early, before they reach users.
