Improve webpack's HTML and CSS experiment support to treat HTML and CSS files as first-class module types. Ensure that enabling these experiments allows HTML and CSS files to be used directly as entry points, and update the CLI documentation accordingly.

*   Update CLI Documentation:
    *   Modify the HTML experiment flag description to: "Enable HTML entry support. Treats `.html` files as a first-class module type so they can be used directly as entry points."

*   Resolve Configuration Adjustments:
    *   Include `.html` in the extension lookup lists when `experiments.futureDefaults` is enabled, placing it before standard JS extensions.
    *   Include `.css` in the extension lookup lists when the CSS experiment is enabled, appending it after JS extensions.
    *   Ensure combined extension order is `.html`, JS/TS extensions, then `.css` when both experiments are enabled.

*   Module Parser and Rules Configuration:
    *   Add an entry for the HTML module type with `sources` set to `true` in the default module parser configuration.
    *   Include a `resolve` configuration block in CSS module rules with `fullySpecified` and `preferRelative` settings.
    *   Emit TypeScript-related module rules in the order: `.mts` and its ESM `descriptionData` sibling, followed by `.ts`, and then `.cts` with its CommonJS `descriptionData` sibling.

*   Entry Point Resolution:
    *   Resolve an extensionless entry point to a CSS index file when `experiments.css: true` is configured, producing an output CSS bundle.
    *   Resolve an extensionless entry point to an HTML index file when `experiments.html: true` is configured, prioritizing `.html` over `.js`.

*   HTML Entry Point Processing:
    *   When an HTML file is used as an entry point with both `experiments.html: true` and `experiments.css: true`, replace `<script src>` and `<link rel="stylesheet">` references with emitted JS and CSS chunk file references in the output HTML. Ensure the JS chunk has a `.js` extension and the CSS chunk has a `.css` extension with original stylesheet rules.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.