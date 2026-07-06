Implement a fix for the Avatar component to ensure the fallback person silhouette icon is visible in High Contrast mode. Update the component to render the icon directly as an SVG element without any intermediate wrapper, allowing it to inherit theme colors correctly.

*   Update the AvatarSlotProps interface in `Avatar.types.ts`:
    *   Add a `fallbackIcon` field typed as `SvgProps` from `react-native-svg`.
*   Modify the Avatar component in `Avatar.tsx`:
    *   Register `fallbackIcon` in the slot map using the native `Svg` component from `react-native-svg`.
    *   Ensure the `renderAvatar` function uses `Slots.fallbackIcon` with `viewBox="0 0 14 16"` when `svgIconsEnabled` is true and neither initials nor an explicit icon are provided.
*   Adjust the Avatar styling settings in `Avatar.styling.ts`:
    *   Add a `fallbackIcon` entry using `buildProps` to apply `color`, `width`, and `height` from the `AvatarTokens`.
    *   Use `iconColor || color` for the color and `iconSize` for the dimensions, observing the tokens `['iconSize', 'iconColor']`.
*   Ensure the fallback icon:
    *   Renders as a direct SVG element with numeric width and height from the `iconSize` token.
    *   Inherits color and tintColor properties from the theme, without any intermediate View element.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.