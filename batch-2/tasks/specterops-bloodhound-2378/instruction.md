I'm working on the extension management UI and need to add the ability to delete custom extensions.

*   The Extension type must include an `is_builtin` boolean field to distinguish built-in extensions (true) from user-created custom extensions (false).

*   The `ActiveExtensionsCard` component must render a delete button for each extension row. Each button must have an accessible label of the form 'Delete {extensionName}' (e.g., 'Delete Custom Extension').

*   Delete buttons for extensions where `is_builtin` is true must be disabled. Delete buttons for extensions where `is_builtin` is false must be enabled.

*   Clicking an enabled delete button must open a confirmation dialog that contains the title 'Delete selected extension', body text matching 'This will permanently delete the selected extension', a warning matching 'Warning: This change is irreversible', and a text input whose placeholder matches the extension's name.

*   The confirmation dialog must include a cancel button (labeled 'cancel') that closes the dialog when clicked.

*   The confirm button in the dialog must be disabled initially and remain disabled while the input value does not exactly match the extension's name. It must become enabled only when the exact extension name is typed.

*   After the confirmation dialog is closed (via cancel) and reopened, the text input must be empty (reset to an empty string).

*   When the confirm button is clicked with the correct name entered, `apiClient.deleteExtension` must be called with the extension's `id` string.

*   After a successful deletion, the notification system must be called with the message 'Extension "{name}" was deleted successfully!' (where {name} is the extension name), the notification ID 'deleteExtensionSuccess', and options `{ anchorOrigin: { horizontal: 'right', vertical: 'top' } }`.

*   After a failed deletion, the notification system must be called with the message 'Failed to delete extension "{name}". Please try again.' (where {name} is the extension name), the notification ID 'deleteExtensionError', and options containing `{ variant: 'error', anchorOrigin: { horizontal: 'right', vertical: 'top' } }`.

*   The API client must expose a `deleteExtension(extensionId: string)` method that sends a DELETE request to `/api/v2/extensions/{extensionId}` and expects a 204 response.


*   Interface details: Type: Type
Name: Extension
Location: packages/javascript/js-client-library/src/responses.ts
Description: The Extension type must include an `is_builtin: boolean` field in addition to existing fields (`id`, `name`, `version`). This field is used to determine whether an extension can be deleted.

---

Type: Method
Name: deleteExtension
Location: packages/javascript/js-client-library/src/client.ts
Signature: deleteExtension(extensionId: string, options?: RequestOptions): Promise<AxiosResponse<void>>
Description: Sends a DELETE request to `/api/v2/extensions/{extensionId}`. The method is accessed via the `apiClient` instance exported from `packages/javascript/bh-shared-ui/src/utils`. Tests spy on this method using `vi.spyOn(apiClient, 'deleteExtension')`.

---

Type: Component
Name: ActiveExtensionsCard
Location: packages/javascript/bh-shared-ui/src/views/OpenGraphManagement/ActiveExtensionsCard.tsx
Description: The existing ActiveExtensionsCard component must be updated to render per-row delete buttons with aria-label "Delete {extensionName}", open a confirmation dialog on click (disabled for built-in extensions), validate input against the extension name before enabling confirmation, call `apiClient.deleteExtension` with the extension ID on confirm, and invoke `addNotification` from `useNotifications()` with specific messages on success and error.

The confirmation dialog must display:
- Title: "Delete selected extension"
- Body text containing: "This will permanently delete the selected extension"
- Warning text containing: "Warning: This change is irreversible"
- A text input with the extension name as its placeholder

The notification calls must use these exact signatures:
- Success: `addNotification('Extension "{name}" was deleted successfully!', 'deleteExtensionSuccess', { anchorOrigin: { horizontal: 'right', vertical: 'top' } })`
- Error: `addNotification('Failed to delete extension "{name}". Please try again.', 'deleteExtensionError', { variant: 'error', anchorOrigin: { horizontal: 'right', vertical: 'top' } })`

where `{name}` is replaced with the actual extension name.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.