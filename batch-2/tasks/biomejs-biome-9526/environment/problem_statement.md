## Description

The lint rule that enforces type-only imports is producing a false positive in Svelte files. When a function is imported and used in two ways — once in the script block to describe a type (via a TypeScript utility that extracts parameter types), and once in the HTML template as an actual function call — the rule incorrectly flags the import as needing to be a type-only import.

## Expected Behavior

- When an imported symbol is called as a function inside a Svelte template's attribute expression, the rule should recognize this as a runtime value usage.
- The rule should produce no diagnostic (no warning or suggestion) for imports that are used as values in the template, even if those same imports also appear in type positions within the script block.

## Why This Matters

If a developer follows the rule's suggestion and changes the import to a type-only import, the function call in the template will break at runtime because type-only imports are erased before the code executes. The rule must not suggest converting imports to type-only when they are needed at runtime in the template.
