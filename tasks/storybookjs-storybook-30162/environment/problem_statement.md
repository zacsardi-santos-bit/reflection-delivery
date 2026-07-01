## Description

When users have both the accessibility addon and the test addon installed and run the automated migration tool, only the Vitest setup file gets updated — the project preview configuration file is left untouched. This means users still have to manually add the tag that controls which stories get accessibility-tested in CI runs, which is easy to miss and causes confusion about why accessibility checks aren't running.

## Expected Behavior

- The migration tool should also update the preview configuration file to add the tag that enables accessibility testing for stories.
- If the preview file already contains the necessary tag, the tool should detect this and skip that step instead of redundantly transforming the file.
- Similarly, if the Vitest setup file already has the accessibility addon import, that step should be skipped.
- The tool should only return "no migration needed" when BOTH files are already properly configured.
- Prompt messages shown to the user should clearly distinguish between automatic and manual steps, numbering them sequentially and only showing the steps that are actually required.
- Running the migration should write the updated preview configuration to disk, just as it does for the Vitest setup file.

## Why This Matters

Without this change, setting up the accessibility/test integration always requires at least one manual step — updating the preview file — even when the tool could handle it automatically. Users who miss this step end up with an incomplete setup where accessibility tests don't run as expected in CI, leading to hard-to-diagnose failures.
