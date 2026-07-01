I'm working on the Volto block styling system and have run into a couple of issues that need fixing.

First, the existing function that builds CSS class names from a style data object is incorrectly including CSS custom property entries in the output. Custom properties should never generate class names — only regular style properties should. The class-name builder needs to be updated to skip custom property keys entirely.

Second, we need a new utility function that does the opposite: given a style data object, extract only the custom property entries into a flat object so they can be applied as inline styles. This function should also handle nested style objects by flattening them — nested custom properties should be included in the output with a key that reflects their full path through the nesting levels. Non-custom-property keys at any nesting level should be ignored.

Finally, the Sitemap component needs to be updated to work correctly when the site is configured for multiple languages. Currently it doesn't render properly in that context.
