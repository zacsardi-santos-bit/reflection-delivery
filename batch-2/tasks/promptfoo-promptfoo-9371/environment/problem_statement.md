## Description

The evaluation results table supports deep-linking: users can share a URL that points directly to a specific test result row. When the page loads with such a URL, the table should automatically paginate to the page containing the target row and open its detail view. However, there is a bug when a filter (such as "show only failures") is active at the same time as a deep link.

## The Problem

The URL hash format for deep links encodes the row's **global** position across all unfiltered results. When a filter is active, the table operates on a filtered subset of results — and filtered pagination uses positions within that subset, not global indices. The bug causes the table to treat the global index from the hash as if it were a filtered-table position, which navigates to the wrong page.

Additionally, when a filter is later removed after suppressing a hash-based deep link, the previously-ignored deep link parameters can sometimes be re-read and trigger an incorrect navigation — instead of simply resetting to the first page.

## Expected Behavior

- When a filter is active, hash-only deep links should **not** be used to compute which page to navigate to. The hash should still be preserved in the URL so the target row's detail view can open if that row happens to appear on the current filtered page.
- The legacy row-ID query parameter (which already encodes a filtered position) should continue to work for page navigation even with filters active.
- After a filter is removed, any cleared deep-link parameters should not re-trigger navigation — the table should stay on or reset to the first page.
- The above behavior must be stable even in React's development Strict Mode, which replays effects and previously caused deep-link navigation to be incorrectly suppressed.

## Why This Matters

Users who apply filters and also land on deep-linked URLs will be taken to the wrong page. This makes the deep-linking feature unreliable, especially when shared links are opened in a filtered view.
