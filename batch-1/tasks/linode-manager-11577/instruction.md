Implement a feature to enhance the alerts listing page by adding search and filtering capabilities, clickable alert names, and visual status indicators. Break out the table into a reusable component that handles alerts, loading states, errors, and service lists.

*   Update the `AlertsListTable` component:
    *   Render column headers: 'Alert Name', 'Service', 'Status', 'Last Modified', and 'Created By'.
    *   Accept an optional `error` prop (array of objects with a 'reason' string field) and display the reason text when present.
    *   Render each alert's label, service display name (resolved from the `services` prop), capitalized status string, `created_by` value, and updated timestamp formatted as 'MMM dd, yyyy, h:mm a'.

*   Update the `AlertTableRow` component:
    *   Accept a `services` prop of type `Item<string, AlertServiceType>[]` to display the human-readable service label.
    *   Render the alert label as a hyperlink with `href` set to `${location.pathname}/detail/${service_type}/${id}`.
    *   Include a status indicator element with `data-testid='status-icon'` and a background color of `rgb(0, 176, 80)` for 'enabled' status.
    *   Render an action menu labeled `Action menu for Alert {alert.label}` with a 'Show Details' item accessible via `data-testid='Show Details'`.

*   Update the `AlertListing` component:
    *   Include a service filter dropdown with `data-testid='alert-service-filter'` that loads options from the `useCloudPulseServiceTypes` query and filters alerts by `service_type`.
    *   Include a status filter dropdown with `data-testid='alert-status-filter'` with options 'Enabled' and 'Disabled' to filter alerts by status.
    *   Include a text search input with placeholder 'Search for Alerts' that filters alerts by name (case-insensitive).
    *   Render an `AlertsListTable` with the filtered alerts.

*   Ensure the `Item` type alias is used for select/autocomplete options in the `services` and `status` filters.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.