Update the admin metrics page to display human-readable metric names instead of raw identifiers. Ensure that filtering and dropdown selection features continue to function correctly with the new names, and exclude specific subsystem metrics from display. Implement a reusable paginated table component for use across the application.

*   Modify the Metrics component:
    *   Display human-readable names for metrics:
        *   'queries.number' as 'Number of Queries'
        *   'threads.daemon' as 'Daemon Threads'
        *   'threads.total' as 'Total Threads'
        *   'users' as 'Users'
        *   'users.active' as 'Active Users'
        *   'users.active.total' as 'Total Active Users'
        *   'requests.active' as 'Active Requests'
        *   'requests.exceptions' as 'Request Exceptions'
        *   'requests.response-time' as 'Request Response Time'
    *   Exclude metrics with keys starting with 'auth', 'multiprocessing', or 'python.gc'.

*   Implement the PaginatedTable component in `desktop/core/src/desktop/js/reactComponents/PaginatedTable/PaginatedTable.tsx`:
    *   Accept the following props:
        *   `data`: array of records
        *   `columns`: Ant Design ColumnProps array
        *   `rowKey`: function returning a string key per record or a string
        *   `testId`: string rendered as data-testid on the wrapped table element
        *   Optional `onRowSelect`: callback for selected rows
        *   Optional `onRowClick`: callback for row clicks
        *   Optional `pagination`: controls pagination behavior
    *   Render the table with a `data-testid` attribute equal to the `testId` prop value.
    *   Render data rows provided in the `data` prop.
    *   Implement row selection:
        *   Render a "select all" checkbox at DOM index 0 when `onRowSelect` is provided and data is non-empty.
        *   Call `onRowSelect` with an array of selected records when a row checkbox is clicked.
    *   Implement row click handling:
        *   Call `onRowClick` with the record when a row is clicked.
        *   Invoke the returned onClick handler on row click.
    *   Implement pagination:
        *   Render an element with CSS class 'hue-pagination' when `pagination.pageStats.totalPages` is greater than 0.
        *   Do not render the 'hue-pagination' element when `pagination.pageStats.totalPages` is 0.
    *   Ensure the `pagination` prop accepts the shape:
        *   `pageSize`: number
        *   `setPageSize`: function
        *   `setPageNumber`: function
        *   `pageStats`: object with `totalPages`, `pageNumber`, `pageSize`, `totalSize`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.