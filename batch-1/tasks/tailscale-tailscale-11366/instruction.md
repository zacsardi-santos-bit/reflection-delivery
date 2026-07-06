Implement improvements to the TailFS share management API to ensure shares are consistently ordered, support renaming, and provide clear error handling. Update the internal storage and notification systems to accommodate these changes.

*   Modify the Prefs struct:
    *   Include a TailFSShares field as a slice of Share pointers, sorted alphabetically by name.
    *   Register this field in the known prefs field enumeration, but exclude it from the CLI flag in the 'up' command.

*   Export error values from the ipnlocal package:
    *   ErrInvalidShareName for invalid share names.
    *   ErrTailFSNotEnabled for when TailFS sharing is not enabled.

*   Update TailFS share management methods:
    *   TailFSSetShare:
        *   Normalize share names by trimming whitespace and converting to lowercase.
        *   Replace existing shares with the same normalized name or insert new ones alphabetically.
        *   Return ErrInvalidShareName for invalid names and ErrTailFSNotEnabled if sharing is disabled.
        *   Send an ipn.Notify with the updated TailFSShares slice on success.
    *   TailFSRemoveShare:
        *   Remove shares by name and return os.ErrNotExist if the share does not exist.
        *   Return ErrTailFSNotEnabled if sharing is disabled.
        *   Send an ipn.Notify with the updated TailFSShares slice on success.
    *   TailFSRenameShare:
        *   Normalize the new name and ensure it is unique.
        *   Return os.ErrNotExist if the old name is not found, os.ErrExist if the new name exists, and ErrInvalidShareName if the new name is invalid.
        *   Return ErrTailFSNotEnabled if sharing is disabled.
        *   Re-sort the shares list and send an ipn.Notify with the updated TailFSShares slice on success.

*   Update internal storage and notification systems:
    *   Use a sorted slice instead of a map for shares in the Notify struct's TailFSShares field.
    *   Ensure the FileSystemForRemote interface's SetShares method accepts a sorted slice of Share pointers.
    *   Implement CompareShares in the tailfs package to sort Share slices alphabetically by name.

*   Ensure notifications are sent to WatchNotifications listeners after successful share management operations with the updated, sorted list of shares.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.