Update the Badge component to ensure it adapts to content size and applies consistent font styling. Implement a minimum height for the badge container and define explicit font properties for the text inside the badge.

*   Modify the Badge container:
    *   Use `minHeight` instead of `height` for all size variants.
    *   Ensure the 'large' size has `minHeight: 24` and does not include a `height` property.

*   Update the `BadgeCoreTokens` interface:
    *   Add an optional `textPadding` property of type `number` to control horizontal padding on the badge's text element.

*   Revise the `defaultBadgeTokens` configuration:
    *   Define `minHeight` for each size variant: tiny=6, extraSmall=10, small=16, medium=20, large=24, extraLarge=32.
    *   Include default font token values:
        *   fontSize: 10 (overridden per size)
        *   fontFamily: resolved from the theme's primary typography family
        *   fontWeight: semibold (resolving to '600')
    *   Specify per-size fontSize values: tiny=4, extraSmall=6, small=8, medium=10, large=12, extraLarge=12.
    *   Include `textPadding` at the default and per-size levels, with `textPadding` for 'large', 'medium', 'small', and 'extraLarge' set to 2.

*   Adjust the badge text slot styling:
    *   Apply explicit font styling props directly on the Text element: `fontFamily`, `fontSize`, `fontWeight`.
    *   For the 'large' size, ensure these resolve to:
        *   fontFamily: 'Segoe UI'
        *   fontSize: 12
        *   fontWeight: '600'
    *   Apply `paddingHorizontal` using the `textPadding` token value.

*   Update the styling configuration:
    *   Ensure the text slot tracks font-related token keys (fontFamily, fontSize, fontWeight) and `textPadding` as dependency keys.
    *   Ensure the root/container slot uses `minHeight: tokens.minHeight` instead of `height: tokens.height`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.