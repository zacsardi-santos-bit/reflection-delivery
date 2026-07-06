Implement a new FontIcon component in the experimental icon package to render icons from custom or built-in fonts. Ensure it accepts specific properties and renders consistently as a plain text element.

*   Create the FontIcon component in `packages/experimental/ExperimentalIcon/src/FontIcon/FontIcon.tsx`.
    *   Implement the component with the signature: `FontIcon(props: FontIconProps) => JSX.Element`.
    *   Accept the following props:
        *   `codepoint` (required, number)
        *   `color` (optional, string/ColorValue)
        *   `fontFamily` (optional, string)
        *   `fontSize` (optional, number)
    *   Render a React Native `<Text>` element with:
        *   Text content as the Unicode character corresponding to the `codepoint`.
        *   A style object containing `color`, `fontFamily`, and `fontSize`.
            *   Ensure `fontSize` is included in the style object even if undefined.

*   Ensure the FontIcon component renders identically across multiple sequential renders with the same props.
*   Define the `FontIconProps` interface in `packages/experimental/ExperimentalIcon/src/FontIcon/FontIcon.types.ts`.
    *   Include the following properties:
        *   `codepoint` (number, required)
        *   `color` (optional, ColorValue)
        *   `fontFamily` (optional, string)
        *   `fontSize` (optional, number)
        *   `fontSrcFile` (optional, string)
        *   `style` (optional, StyleProp<TextStyle>)

*   Export FontIcon and FontIconProps from `packages/experimental/ExperimentalIcon/src/index.ts` to make them available for import by consumers.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.