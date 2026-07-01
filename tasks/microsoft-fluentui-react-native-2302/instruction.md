Implement the missing foreground color tokens in the Android theme to ensure components can correctly style disabled states and content on colored backgrounds. Update the type definitions and theme mapping to include these tokens, respecting light and dark appearances.

*   Update the Android theme color token object:
    *   Add `neutralForegroundDisabled1` token:
        *   Use '#5c5c5c' for light, highContrast, and dynamic appearances.
        *   Use '#bdbdbd' for dark appearance.
    *   Add `neutralForegroundDisabled2` token:
        *   Use '#2e2e2e' for light, highContrast, and dynamic appearances.
        *   Use '#ffffff' for dark appearance.
    *   Add `neutralForegroundOnColor` token:
        *   Use '#000000' for light, highContrast, and dynamic appearances.
        *   Use '#ffffff' for dark appearance.

*   Update the `AliasColorTokens` type definition in `packages/theming/theme-types/src/Color.types.ts`:
    *   Declare `neutralForegroundDisabled1`, `neutralForegroundDisabled2`, and `neutralForegroundOnColor` as optional fields:
        *   `neutralForegroundDisabled1?: ColorValue;`
        *   `neutralForegroundDisabled2?: ColorValue;`
        *   `neutralForegroundOnColor?: ColorValue;`

*   Update the `mapPipelineToTheme` function in `packages/theming/theming-utils/src/mapPipelineToTheme.android.ts`:
    *   Map the pipeline output's `neutralForegroundDisabled1`, `neutralForegroundDisabled2`, and `neutralForegroundOnColor` entries to the corresponding fields in the `AliasColorTokens` object.
    *   Use the `fillColorRest` property from each pipeline output entry:
        *   `pipelineOutput.neutralForegroundDisabled1.fillColorRest`
        *   `pipelineOutput.neutralForegroundDisabled2.fillColorRest`
        *   `pipelineOutput.neutralForegroundOnColor.fillColorRest`

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.