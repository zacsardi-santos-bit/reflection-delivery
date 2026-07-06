I'm working on the network tab in the developer tools console of a desktop API client.

*   The NetworkTab component must render clickable column headers for at least: Method (data-testid='network-header-method'), Status (data-testid='network-header-status'), and Duration (data-testid='network-header-duration'). All column header elements must be clickable to trigger sorting.

*   By default (no column clicked), no sort indicator icon must be visible in the component; neither an ascending icon (data-testid='sort-icon-asc') nor a descending icon (data-testid='sort-icon-desc') should be present.

*   Clicking a column header for the first time must activate ascending sort and render an element with data-testid='sort-icon-asc'. No descending icon must be visible.

*   Clicking the same column header a second time must switch to descending sort: an element with data-testid='sort-icon-desc' must appear, and the ascending icon must not be visible.

*   Clicking the same column header a third time must clear the sort: neither sort icon must be present in the DOM.

*   Clicking a different column header while another column is already sorted must reset to ascending on the newly clicked column, showing only data-testid='sort-icon-asc'. The previous column's descending icon must not be visible.

*   Only one sort icon must be rendered at a time regardless of how many columns exist — the sort icon appears only on the currently active sort column.

*   When sorted ascending by method, request rows (data-testid='network-request-row') must appear in alphabetical A-to-Z order by HTTP method. Sorting must be case-insensitive (e.g., 'post', 'GET', and 'delete' sort as if all uppercase).

*   When sorted descending by method, request rows must appear in reverse alphabetical Z-to-A order, also case-insensitively.

*   When sorted ascending by status, request rows must appear ordered by numeric HTTP status code from lowest to highest (e.g., 200 before 404 before 500). Status code must be read from the response's statusCode field.

*   When the sort is cleared (third click), the request rows must return to their original insertion order as provided by the store.

*   Each request row rendered by the component must contain a child element with CSS class 'method-badge' (text content: the HTTP method, rendered uppercase) and a child element with CSS class 'status-badge' (text content: the numeric status code).

*   The component must read request data from the Redux store at state.collections.collections (array of collection objects, each having a 'timeline' array of request entries) and filter state from state.logs.networkFilters and state.logs.selectedRequest.


*   Interface details: Type: Component
Name: NetworkTab
Location: packages/bruno-app/src/components/Devtools/Console/NetworkTab/index.js
Description: A React component that renders the network request list in the developer tools console. It reads from the Redux store (state.collections.collections for request timelines, state.logs.networkFilters and state.logs.selectedRequest for filter/selection state). Renders clickable column headers and sortable request rows.

Required data-testid attributes on rendered DOM elements:
- "network-header-method" — the Method column header element (clickable)
- "network-header-status" — the Status column header element (clickable)
- "network-header-duration" — the Duration column header element (clickable)
- "sort-icon-asc" — icon rendered on the active column when sorted ascending
- "sort-icon-desc" — icon rendered on the active column when sorted descending
- "network-request-row" — each individual request row element

Required CSS classes on child elements within each request row:
- "method-badge" — renders the HTTP method text (uppercase)
- "status-badge" — renders the numeric HTTP status code text

Sort state behavior:
- Default: no sort active (neither sort icon is in the DOM)
- First click on a column header: sort ascending (sort-icon-asc present, sort-icon-desc absent)
- Second click on the same column header: sort descending (sort-icon-desc present, sort-icon-asc absent)
- Third click on the same column header: sort cleared (neither sort icon present)
- Click on a different column while sorted: ascending sort on new column (sort-icon-asc present)
- Only one sort icon exists in the DOM at any time

Sort semantics:
- Method column: case-insensitive string comparison (toUpperCase before comparing)
- Status column: numeric comparison using statusCode field from response data
- When sort is cleared: original insertion order restored

Redux store shape required:
- state.collections.collections: array of { uid: string, name: string, timeline: RequestEntry[] }
- state.logs.networkFilters: { GET: boolean, POST: boolean, PUT: boolean, DELETE: boolean, PATCH: boolean, HEAD: boolean, OPTIONS: boolean }
- state.logs.selectedRequest: null or RequestEntry

RequestEntry shape:
{
  type: 'request',
  timestamp: number,
  collectionUid: string,
  itemUid: string,
  collectionName: string,
  data: {
    request: { method: string, url: string },
    response: { status: number, statusCode: number, duration: number, size: number },
    timestamp: number
  }
}


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.