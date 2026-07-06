## Description

The bulk rename feature for network interfaces should support renaming multiple fields — both the interface name and the interface label — in a single operation. Currently, the interface bulk rename view only targets a single implicit field. There is no way for users to choose whether they want to rename the name, the label, or both fields simultaneously.

## Expected Behavior

- When performing a bulk rename on interfaces, users should be able to select which field(s) they want to apply the find/replace operation to (name, label, or both).
- If a user initiates a bulk rename without selecting any fields, the operation should be rejected with a validation error prompting them to select at least one field.
- The preview step should correctly show all matching interface objects when a valid field selection is provided, even when the selection spans multiple pages.
- The apply step should successfully rename interfaces using the provided find/replace pattern when the field selection is provided, and redirect the user on completion.

## Why This Matters

Network administrators frequently need to rename interfaces in bulk. Supporting renaming of both the name and label fields in a single operation reduces manual effort and makes bulk operations more flexible. Requiring explicit field selection also makes the operation's intent clear and prevents unintended modifications.
