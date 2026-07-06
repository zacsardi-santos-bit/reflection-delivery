## Description

The enhanced image library currently uses a Svelte preprocessor to transform custom image tags into optimized picture elements at build time. While this works for templates, it has a significant limitation: developers cannot style the custom image element using a CSS type selector in their component's style block. The colon in the tag name causes issues, and the preprocessor makes no attempt to transform CSS rules that target the custom tag.

The solution is to migrate the image tag processing from a Svelte preprocessor to a Vite plugin transform. The Vite transform approach processes the full component file, allowing it to handle both the HTML template (replacing the custom image tags with proper picture elements) and the component's CSS block (rewriting any selectors that target the custom image element by its tag name so that they correctly apply to the rendered element).

## Expected Behavior

- The enhanced image tag processing continues to work as before, converting custom image tags into picture elements with multiple source formats
- When a component's style block contains a CSS rule that uses the custom image element's tag name as a selector (with the colon properly escaped), the build tool should rewrite that selector so it applies to the rendered element
- The image processing module should be refactored into a new file that exports the Vite plugin factory and the object-parsing utility

## Why This Matters

Previously, developers had to add class names to their enhanced image tags and use class selectors in CSS to style them — there was no way to use a type selector. This change makes styling more natural: you can now write CSS targeting the enhanced image element by its tag name directly in the component's style block.
