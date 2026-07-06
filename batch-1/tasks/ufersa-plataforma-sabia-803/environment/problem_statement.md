## Description

The technology detail page includes a table that displays the persons responsible for a technology (owners and contributors). Currently, this table always shows a column for the academic profile identifier (a Brazilian academic registry ID), regardless of where the table is being shown. In some contexts — such as the review/summary step of the technology registration form — this column is not relevant or appropriate and should be hidden.

Additionally, when there are no responsible persons to display, the table body doesn't render correctly: instead of showing an empty table body, it outputs incorrect content.

## Expected Behavior

- The responsibles table component should support an optional flag that hides the academic profile identifier column (both the header and all associated data cells in each row).
- When this flag is set, the table should only show the Name, Email, Phone, and Registration Status columns.
- When no responsible persons are provided, the table body should be empty (no rows rendered).
- When responsible persons are provided (without the hide flag), the table should show all columns including the academic profile identifier as a link.

## Why This Matters

Different parts of the platform need to display the list of responsible persons in different ways. The review step should show a simplified view without the academic profile column, while the full technology detail page should still display all information. Without this flexibility, the same hard-coded table layout is forced into contexts where it doesn't fit.
