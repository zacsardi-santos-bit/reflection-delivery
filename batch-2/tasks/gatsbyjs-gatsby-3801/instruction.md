Implement the ability for the Gatsby Less plugin to accept a theme configuration for Less variable overrides during the build process. Ensure that the plugin can handle both direct JavaScript object inputs and file path inputs for theme configurations, while maintaining existing functionality when no theme is provided.

*   Update the `modifyWebpackConfig` function in `packages/gatsby-plugin-less/src/gatsby-node.js`:
    *   Accept a second argument `options`, which may include a `theme` property.
    *   Ensure the function returns the same `config` object reference that was passed in.
    *   Handle `options.theme` as follows:
        *   If `options.theme` is a JavaScript object, serialize it to JSON for use in Less loader strings.
        *   If `options.theme` is a string file path, require the file and use its exported object as the theme.
        *   If `options` contains no `theme` property, proceed without applying variable overrides.
*   Configure Less loader strings based on the build stage:
    *   For the `develop` stage:
        *   With theme: `less?{"sourceMap":true,"modifyVars":<themeJson>}`
        *   Without theme: `less?{"sourceMap":true}`
        *   Apply the loader string to both `less` and `lessModules` loaders.
    *   For `build-css` stage:
        *   With theme: `less?{"modifyVars":<themeJson>}`
        *   Without theme: `less`
        *   Apply the loader string to both `less` and `lessModules` loaders.
    *   For `develop-html`, `build-html`, and `build-javascript` stages:
        *   With theme: `less?{"modifyVars":<themeJson>}`
        *   Without theme: `less`
        *   Apply the loader string only to the `lessModules` loader.
*   Create a file `packages/gatsby-plugin-less/src/theme-test.js`:
    *   Export a CommonJS object containing `{'text-color': '#fff'}` for testing purposes.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.