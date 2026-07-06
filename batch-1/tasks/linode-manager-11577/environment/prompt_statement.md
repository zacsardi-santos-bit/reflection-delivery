I'm working on the alerts listing page in our cloud monitoring UI. Right now the page just shows a raw table of all alerts with no way to filter or search — when you have hundreds of alerts across different services, it's really hard to find what you're looking for.

I need to add search and filtering functionality to this page. Specifically, there should be a text search input (with placeholder "Search for Alerts") that filters the displayed alerts by their name as you type. There should also be a service type filter dropdown that lets users select one or more services and see only alerts for those services, and a status filter dropdown with "Enabled" and "Disabled" options.

On top of that, the alert table itself needs some improvements. The alert name in each row should be a clickable link that goes directly to the alert's detail page. The status column should show a colored icon — green for enabled alerts — so users can visually scan the status quickly. The table should also show the service display name in the Service column (not just the raw service type code), and the date should be in a human-readable format.

I also need to break out the table into its own reusable component that accepts alerts, a loading state, an optional error, and a services list as props, so the listing page component can focus on the filtering logic.
