Implement a utility function to manage download button states for Electron versions based on platform and architecture. Ensure that on Apple Silicon Macs, download buttons for Electron versions below 11 are disabled. Integrate this logic into the Electron settings panel and version selector dropdown.

*   Implement the `disableDownload` function in `src/utils/disable-download.ts` with the signature `disableDownload(version: string) -> boolean`.
    *   Return `false` for all version strings when the platform is Windows or Linux.
    *   Return `false` for all version strings when the platform is macOS but the architecture is not arm64.
    *   Return `true` for versions below 11.0.0 when the platform is macOS and the architecture is arm64.
    *   Return `false` for versions 11.0.0 and above when the platform is macOS and the architecture is arm64.

*   Update the ElectronSettings component in `src/renderer/components/settings-electron.tsx`.
    *   Import `disableDownload` from `src/utils/disable-download`.
    *   Apply the CSS class 'disabled-version' to version entries where `disableDownload` returns `true`.

*   Update the `renderItem` function in `src/renderer/components/version-select.tsx`.
    *   Import `disableDownload` from `src/utils/disable-download`.
    *   Include an element with the CSS class 'disabled-menu-tooltip' when `disableDownload` returns `true` for the item's version.
    *   Ensure no such element is present when `disableDownload` returns `false`.

*   Implement test utilities in the test utility file.
    *   Export an `overrideArch(value: string)` function to override `process.arch`.
    *   Export a `resetArch()` function to restore `process.arch` to its original value.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.