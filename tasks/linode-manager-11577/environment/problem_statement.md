## Description

The alerts listing page currently displays all alerts in a flat table with no way to narrow down results. Users who manage many alerts across multiple services have no way to quickly find alerts matching a specific service type, status, or name. Additionally, alert names in the table are plain text rather than links, so navigating to an alert's detail page requires extra steps via an action menu. The status column also offers no quick visual cue distinguishing enabled from disabled alerts.

## Expected Behavior

- The alerts table must display column headers for: Alert Name, Service, Status, Last Modified, and Created By.
- Alert names in the table should be clickable links that navigate directly to the alert's detail page.
- A service filter should appear above the table, allowing users to select one or more service types and see only alerts associated with those services.
- A status filter should allow users to filter alerts by their current status (enabled or disabled).
- A search input with the placeholder "Search for Alerts" should allow users to type a name and see only alerts whose name matches.
- The status column should include a colored indicator — green for enabled alerts — to make status immediately visible at a glance.
- If there is an error loading alerts, the error message should be visible in the table area.

## Why This Matters

Without filtering and search, the listing page becomes unusable at scale. Users need to be able to quickly locate specific alerts and navigate to their detail pages without relying solely on an action menu.
