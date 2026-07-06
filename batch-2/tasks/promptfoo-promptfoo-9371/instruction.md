I'm working on an evaluation results table that supports deep-linking to a specific row via a URL hash.

*   When a filter is active (filterMode is set to a value other than 'all'), hash-based deep links (URL hash format #details-row-{N}-prompt-{M}) must NOT be used to compute a page index or trigger navigation to a different page.

*   When a filter is active and a hash-only deep link is present, the URL hash must be preserved unchanged in window.location.hash so that the target row's detail dialog can still open if the row appears on the current page.

*   When no filter is active (filterMode is 'all' or not set), a hash-based deep link must still trigger pagination: fetchEvalData must be called with the correct pageIndex and pageSize to navigate to the page containing the target row.

*   The legacy ?rowId={N} query parameter must continue to trigger correct page navigation even when a filter is active, because rowId encodes a filtered-table position rather than a global index.

*   When a filter is removed after a hash-based deep link was present but not consumed (because filtering was active), the component must clear the URL hash (window.location.hash becomes empty string) and must NOT navigate to the previously deep-linked page.

*   When a filter is removed after a legacy ?rowId= parameter was present and consumed, the component must clear the query parameter (window.location.search becomes empty string) and the subsequent fetchEvalData call must use pageIndex: 0 (reset to first page).

*   When the component is rendered inside React StrictMode (which replays effects), deep-link navigation must still function correctly: fetchEvalData must be called with the correct pageIndex and pageSize, and the URL hash must be preserved.


*   Interface details: Type: Component
Name: ResultsTable
Location: src/app/src/pages/eval/components/ResultsTable.tsx
Description: Results table component that renders evaluation results with support for deep-link row navigation via URL hash (format: #details-row-{globalRowIndex}-prompt-{promptIndex}) and a legacy query parameter (format: ?rowId={rowIndex}). The component accepts a filterMode prop; when filterMode is set to a value other than "all" (for example "failures"), filtering is considered active. When filtering is active, hash-only deep links must NOT be used to determine which page to navigate to, because the hash encodes a global test index that does not map to filtered-table positions. The legacy ?rowId= parameter may still be used for page resolution even when filtering is active. After removing a filter, any previously cleared deep-link parameters must not re-trigger page navigation.
Signature: ResultsTable({ filterMode?: string, ...otherProps }) -> JSX.Element


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.