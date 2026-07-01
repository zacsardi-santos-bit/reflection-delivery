## Description

The edit-profile screen in the mobile app does not currently support custom profile attributes, even when the server has them configured. Users who have custom fields defined for their profile (such as department, phone extension, or any other administrator-defined fields) have no way to view or update those values from the mobile app. This is a gap compared to what the web client offers.

## Expected Behavior

- When custom profile attributes are enabled on a server, the edit-profile form should load and display the current values for all custom attribute fields.
- Each custom attribute should appear as an editable text field alongside the standard profile fields.
- When a user changes a custom attribute value and saves, the updated value should be persisted to the server.
- If custom attributes are disabled on the server, the extra fields should not appear at all.
- When there are no custom attribute fields defined, no extra inputs should be shown.

## Why This Matters

Custom profile attributes allow organizations to capture structured information about users beyond the built-in fields. Without mobile support, users who primarily use the mobile app cannot update this information, and the mobile experience is inconsistent with the web experience. Additionally, the form's field-focus management should scale cleanly as new fields are added dynamically.
