Update the Starlight Tailwind integration package to ensure compatibility with the latest CSS framework version. Modify the package dependencies and adjust the CSS output order to align with the expected behavior for users with newer framework versions.

*   Update `packages/tailwind/package.json`:
    *   Set `tailwindcss` in `devDependencies` to a version compatible with `^3.4.14`.
    *   Set `postcss` in `devDependencies` to a version compatible with `^8.4.47`.

*   Ensure the CSS output order when processing `@tailwind base` with preflight disabled:
    *   Emit the Tailwind CSS custom property block (e.g., `--tw-border-spacing-*`, `--tw-translate-*`, `--tw-rotate`, `--tw-skew-*`, `--tw-scale-*`, ending with `--tw-contain-style`) as the first `*, ::before, ::after` selector block.
    *   Follow with the standard CSS reset block: `*, ::before, ::after { border-width: 0; border-style: solid; border-color: #e5e7eb; }`.
    *   Include `::before, ::after { --tw-content: ; }`, then `html, :host` and `code, kbd, samp, pre` font-family declarations.
    *   Define the `:root` block with Starlight CSS variables such as `--sl-font`, `--sl-font-mono`, `--sl-color-white`, `--sl-color-gray-1` through `--sl-color-gray-6`, `--sl-color-black`, `--sl-color-accent-low`, `--sl-color-accent`, `--sl-color-accent-high`.
    *   Conclude with the `:root[data-theme="light"]` block for light-theme variable overrides.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.