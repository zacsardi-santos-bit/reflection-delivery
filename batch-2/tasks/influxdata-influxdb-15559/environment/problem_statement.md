## Description

When a user exits the configuration editor for a dashboard cell, the cell content briefly disappears and shows a loading indicator before reappearing with the same data that was already loaded. This creates an unnecessary and disruptive flash of loading content even though the view data is already available in the application state.

## Expected Behavior

- If a dashboard cell's view data is already present in the application state, exiting the cell configuration editor should display the cell content immediately without triggering a loading state or re-fetching from the server.
- A network request to retrieve the view should only be made when the view data is not already available locally.
- The loading indicator should only appear when a fetch is actually needed — not as a default step every time the editor is exited.

## Current Behavior

- The cell always resets to a loading state and re-fetches its view from the server when exiting configuration mode, regardless of whether the data is already cached.

## Why This Matters

Users editing dashboard cells experience an unnecessary visual disruption every time they exit the editor. The content they were just looking at flickers through a loading state before reappearing unchanged. By reusing already-loaded view data when available, the transition becomes seamless and the application avoids redundant network requests.
