Add the missing SVG icon files to Gitea's public SVG assets directory to ensure they are available for use in the interface. Ensure that these files are correctly named and accessible.

*   Place the following SVG icon files in the directory `public/assets/img/svg/`:
    *   `octicon-accessibility-inset.svg`
    *   `octicon-ai-model.svg`
    *   `octicon-bookmark-filled.svg`
    *   `octicon-bookmark-slash-fill.svg`
    *   `octicon-file-media.svg`
    *   `octicon-home-fill.svg`
    *   `octicon-tab.svg`
*   Ensure each SVG file is readable on the filesystem:
    *   Use `os.Stat` to verify that there are no errors when checking each file path.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.