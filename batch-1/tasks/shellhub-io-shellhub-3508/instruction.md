Implement a "connection announcement" feature for ShellHub namespaces to allow administrators to set a custom message displayed when a user connects to a device. Update the data model, perform database migrations, and modify the frontend to support this new field.

*   Update the NamespaceSettings struct in `pkg/models/namespace.go`:
    *   Add a `ConnectionAnnouncement` field of type string with JSON and BSON tags set to 'connection_announcement'.
*   Modify namespace creation:
    *   Initialize `ConnectionAnnouncement` to an empty string during system setup.
*   Implement database migration 64:
    *   Create `migration64` in `api/store/mongo/migrations/migration_64.go` with Up and Down functions.
    *   Up function: Use `$set` to initialize 'settings.connection_announcement' to an empty string for namespaces where this field is nil or empty.
    *   Down function: Use `$unset` to remove 'settings.connection_announcement' for namespaces where this field is nil or empty.
    *   Register migration at index 63 in `GenerateMigrations()`.
*   Update the NamespaceEdit Vue component in `ui/src/components/Namespace/NamespaceEdit.vue`:
    *   Render an element with `data-test='namespace-title'` containing `<h3>Namespace</h3>`.
    *   Render a button with `data-test='edit-btn'` labeled "Edit Namespace" that activates edit mode.
    *   Render a save button with `data-test='save-btn'` visible only in edit mode.
    *   Render a text input with `data-test='name-text'` for the namespace name, initially disabled.
    *   Render a textarea with `data-test='connectionAnnouncement-text'` for the connection announcement, initially disabled, with a hint: "A connection announcement is a custom message written during a session when a connection is established on a device within the namespace."
    *   On save, dispatch the Vuex action `namespaces/put` with payload: `{ name: string, id: string, settings: { connection_announcement: string } }`.
    *   On HTTP 403 from the PUT API, dispatch `snackbar/showSnackbarErrorAction` with `INotificationsError.namespaceEdit`.
*   Update the namespace Vuex store module in `ui/src/store/modules/namespaces.ts`:
    *   Expose getters: 
        *   `namespaces/list` → Array (default [])
        *   `namespaces/get` → Object (default {})
        *   `namespaces/getNumberNamespaces` → Number (default 0)
        *   `namespaces/owner` → Boolean (default false)
        *   `namespaces/billing` → Object (default {})
    *   Support mutations:
        *   `namespaces/setNamespaces`: accepts `{ data: Array, headers: { "x-total-count": string } }`, updates the list and count.
        *   `namespaces/setNamespace`: accepts `{ data: namespace_object }`, sets the current namespace.
    *   Ensure the namespace data object includes `connection_announcement` in `settings`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.