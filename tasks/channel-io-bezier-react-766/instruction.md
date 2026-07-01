Implement support for a new 72-pixel avatar size in the Avatar component and adjust the status indicator positioning logic to use the actual avatar size directly. Ensure the status indicator is correctly positioned based on whether a border is shown and update the snapshot tests to reflect these changes.

*   Update the `AvatarSize` enum in `src/components/Avatars/Avatar/Avatar.types.ts`:
    *   Add a new member `Size72` with a numeric value of 72.
    *   Insert `Size72` between `Size48` (48) and `Size90` (90).

*   Adjust the Avatar component rendering logic:
    *   When rendered with a size of `AvatarSize.Size72` and a status indicator:
        *   Position the status wrapper element with `right: 4px` and `bottom: 4px`.
    *   When rendered with `AvatarSize.Size72`, a status indicator, and `showBorder` set to true:
        *   Position the status wrapper element with `right: 8px` and `bottom: 8px`.

*   Modify the status indicator positioning logic:
    *   Base the threshold for applying the larger 4px offset on the avatar's actual size being at least 72 pixels.
    *   Maintain the current -2px offset for avatars with size < 72.

*   Ensure the size value rendered as an attribute on the status wrapper DOM element:
    *   Matches the actual avatar size value (e.g., 24, 72, 90).
    *   Does not use an intermediate or derived size representation from a separate scale.

*   Update the snapshot file at `src/components/Avatars/Avatar/__snapshots__/Avatar.test.tsx.snap`:
    *   Add new snapshot entries for the `Size72` test cases.
    *   Update existing snapshot entries where the status wrapper's size attribute changes to reflect avatar sizes instead of the old internal values.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.