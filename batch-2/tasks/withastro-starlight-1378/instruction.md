Update the Starlight Tailwind integration to use the updated sans-serif font stack from Tailwind CSS 3.4.x. Ensure the generated CSS reflects this change consistently, regardless of Tailwind's CSS reset setting, while keeping other CSS variables unchanged.

*   Modify the Starlight Tailwind integration:
    *   Set the `--sl-font` CSS custom property to `ui-sans-serif, system-ui, sans-serif, "Apple Color Emoji", "Segoe UI Emoji", "Segoe UI Symbol", "Noto Color Emoji"`.
    *   Remove the old font stack values including `-apple-system`, `BlinkMacSystemFont`, `"Segoe UI"`, `Roboto`, `"Helvetica Neue"`, `Arial`, `"Noto Sans"`, and `sans-serif` after `system-ui`.
    *   Ensure the updated `--sl-font` value is used both with and without Tailwind's preflight reset styles.
*   Maintain all other CSS custom properties:
    *   Ensure `--sl-font-mono` and all `--sl-color-*` variables remain unchanged.
*   Update the Tailwind CSS peer dependency:
    *   Set the version to `^3.4.1` or higher in the `@astrojs/starlight-tailwind` package to align with the updated default `fontFamily.sans`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.