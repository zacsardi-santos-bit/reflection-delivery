Implement the necessary changes to improve SVG minification and image compression in the Rsbuild project. Ensure each test case has its own build configuration file, preserve the viewBox attribute during SVG minification, prefix element IDs with a filename-derived prefix, and verify that compressed image files are smaller than their source files.

*   Create a build configuration file for the image-compress test case:
    *   File path: `e2e/cases/image-compress/rsbuild.config.ts`
    *   Configure the image compression plugin to handle JPEG, PNG, ICO, and SVG file types using `pluginImageCompress(['jpeg', 'png', 'ico', 'svg'])` from `@rsbuild/plugin-image-compress`.

*   Ensure the image-compress test case verifies output file sizes:
    *   After building, JPEG, PNG, and SVG files in the `dist` directory must be smaller than their source files in the `src` directory.

*   Create a build configuration file for the svgo-minify-id-prefix test case:
    *   File path: `e2e/cases/svg/svgo-minify-id-prefix/rsbuild.config.ts`
    *   Configure `pluginReact()` from `@rsbuild/plugin-react` and `pluginSvgr({ svgDefaultExport: 'url' })` from `@rsbuild/plugin-svgr`.

*   Ensure the svgo-minify-id-prefix test case processes SVG IDs correctly:
    *   The compiled JavaScript index file must include: `"linearGradient",{id:"idPrefix_svg__a"}`
    *   The source SVG file `e2e/cases/svg/svgo-minify-id-prefix/src/idPrefix.svg` must contain a `linearGradient` element with `id="foo"`, which is renamed and prefixed with "idPrefix_svg__" during processing.

*   Create a build configuration file for the svgo-minify-view-box test case:
    *   File path: `e2e/cases/svg/svgo-minify-view-box/rsbuild.config.ts`
    *   Configure `pluginReact()` from `@rsbuild/plugin-react` and `pluginSvgr({ svgDefaultExport: 'url' })` from `@rsbuild/plugin-svgr`.

*   Ensure the svgo-minify-view-box test case preserves the viewBox attribute:
    *   The compiled JavaScript index file must include: `width:120,height:120,viewBox:"0 0 120 120"`

*   Remove the old combined SVG test file:
    *   File path: `e2e/cases/svg/svgo.test.ts`
    *   Replace with individual test files in `e2e/cases/svg/svgo-minify-id-prefix/index.test.ts` and `e2e/cases/svg/svgo-minify-view-box/index.test.ts`.

*   Implement or verify the existence of the `globContentJSON` function:
    *   Location: `e2e/scripts/helper.ts` or equivalent `@scripts/helper` module
    *   Function signature: `globContentJSON(dir: string) -> Promise<Record<string, string>>`
    *   Functionality: Accepts a directory path and returns a Promise resolving to an object mapping file paths to their contents.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.