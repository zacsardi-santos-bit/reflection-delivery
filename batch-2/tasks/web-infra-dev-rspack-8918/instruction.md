Update the project's package manifests to pin the bundler dependency to the exact newer patch release. Ensure that both the root and test-tools package manifests reflect this change, and verify that the CSS extraction output aligns with the updated dependency paths.

*   Update the webpack bundler dependency:
    *   Change the version in the root `package.json` from `^5.94.0` to `5.95.0`.
    *   Change the version in `packages/rspack-test-tools/package.json` from a loose range to `5.95.0`.

*   Verify CSS extraction output:
    *   Ensure that after updating and reinstalling packages, the CSS extraction output for pathinfo mode includes module source comments with the pnpm virtual store path referencing webpack version `5.95.0` and the peer-dependency hash `_fzyfl4cqgys6tkprvbceqky724`.
    *   Confirm this update is reflected in the CSS files: `style.css`, `other.css`, and `extra.css`.
    *   Ensure the same updated pnpm path references appear in module source comments when devtool source maps are combined with pathinfo mode.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.