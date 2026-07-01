## Description

When a user visits an artist's works-for-sale page on Artsy and applies medium filters (such as selecting paintings, sculptures, or photographs), the browser tab title remains static and unchanged. The title doesn't reflect which mediums are currently active in the filter, making it hard to distinguish filtered views from unfiltered ones in the browser tab or when sharing a URL.

## Expected Behavior

- When no medium filters are applied, the page title should display the default artist title (e.g., "Artist Name - Works for Sale | Artsy").
- When one medium filter is selected, the title should update to reflect that medium (e.g., "Artist Name - Paintings | Artsy").
- When multiple mediums are selected, the title should list them alphabetically with natural language joining (two mediums joined by "and"; three or more using a comma-separated list ending in "and").
- When filters are cleared, the title should revert to the default.
- When the page is loaded with medium filters already present in the URL, the title should reflect those filters on initial render.

## Why This Matters

Users who filter an artist's works by medium can't easily tell from the browser tab what they're viewing, and shared links to filtered views don't produce informative page titles. A dynamic title that reflects the active medium filters makes the browsing experience more informative and improves the utility of sharing filtered views.
