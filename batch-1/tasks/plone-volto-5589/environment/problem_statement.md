## Description

The block styling system supports two distinct ways of applying styles to blocks: class-based styles (which map to CSS class names) and inline custom property styles. When a developer provides a style configuration that mixes both types, the class-name builder currently attempts to generate class names from custom property entries as well. This is incorrect — custom property values should never contribute to class names.

Additionally, there is no existing utility to extract the custom property values from a style object for use as inline styles. A new utility is needed that can handle nested style objects and flatten them into a single-level custom properties map.

Separately, the Sitemap component does not render correctly when the site is configured for multiple languages and needs to be updated to handle that case properly.

## Expected Behavior

- The class-name builder should only generate class names from regular style properties and must skip any custom property entries entirely
- A new utility function should extract all custom property entries from a styles object (including nested objects), producing a flat map with keys derived from the nesting path
- The Sitemap component should render correctly and without errors when multilingual support is enabled in the site configuration

## Why This Matters

Without proper separation, custom property entries end up being incorrectly included when building class names, leading to malformed CSS class names and broken styling. The Sitemap fix ensures that multilingual sites display their navigation tree correctly.
