## Description

Code coverage overlays are not showing up correctly on Windows. The extension normalizes file paths from the test runner to match the lowercase drive letter convention used by the editor's document system — but this normalization is only applied to test result paths, not to coverage map paths. As a result, when a user runs tests with coverage on Windows, the coverage overlay is missing or incomplete because the coverage data paths don't match the editor's file references.

## Expected Behavior

- All file paths coming from the test runner — both test result paths and coverage map paths — should have their Windows drive letters normalized to lowercase before being used by the extension.
- Paths that already use lowercase drive letters should not be modified.
- On non-Windows systems, paths should be returned as-is without any normalization.

## Additional Improvements

- The related types and utilities that deal with test results are currently spread across multiple separate modules, requiring several distinct imports. These should be consolidated into a single unified module so consumers only need to import from one place.
- The internal cache reset logic inside the test result provider should be extracted into a dedicated public method so it can be invoked independently when needed.

## Why This Matters

Windows developers relying on coverage overlays are getting a broken experience with no visible coverage highlighting. The fix ensures consistent path handling across all test result data and makes the codebase easier to maintain with a cleaner module structure.
