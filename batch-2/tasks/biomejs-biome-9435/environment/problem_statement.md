## Description

The lint rule that suggests converting regular imports to type-only imports incorrectly fires for component imports in Astro files when those components are used as rendered elements in the template section.

## Expected Behavior

In Astro files, there are two distinct sections:
- A **frontmatter** block (the script area between the delimiter markers) containing TypeScript/JavaScript code
- A **template** section (the markup area below the frontmatter) where components can be used as rendered elements

When a component is imported in the frontmatter and then used as a rendered element in the template section, the lint rule should recognize this as a real runtime value usage. It should **not** suggest converting the import to a type-only import.

Currently, the rule appears to ignore template section usage, causing it to incorrectly flag these component imports — suggesting they should be type-only imports even though doing so would break the code.

## Why This Matters

Astro files are a popular file format that combines a script block with a template section. Developers commonly import components in the frontmatter and use them in the template. If the linter incorrectly tells them to convert those imports to type-only imports, and they follow that suggestion, their Astro components will break at runtime. The fix should make the linter aware of component usage in the Astro template section so that false positive suggestions are no longer produced.
