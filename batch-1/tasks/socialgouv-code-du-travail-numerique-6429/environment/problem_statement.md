## Description

The home page search component currently requires its parent to pass in a callback function to handle what happens after a user submits a search query. This creates unnecessary coupling: the parent component must know about and manage navigation logic that really belongs inside the search component itself. When the user selects a suggestion from the autocomplete dropdown, the parent callback is responsible for routing them to the results — but this responsibility should live within the search component.

## Expected Behavior

- The search component on the home page should handle its own navigation when a query is submitted, without requiring any callback prop from its parent
- When a user selects a suggestion or submits the search form, the application should automatically redirect to the search results page with the query properly URL-encoded in the address bar
- The search area should use proper semantic form markup so that standard browser form behaviors work correctly (e.g., pressing Enter to submit)
- The visual styling of the autocomplete suggestion dropdowns — both on the home page and in the site header — should be updated to use correct layering depth and spacing values
- The home page title heading layout should be adjusted so the subtitle text flows inline rather than as a block element preceded by a line break

## Why This Matters

Decoupling search navigation from parent components makes the search component more self-contained and easier to reuse. Using proper form semantics also improves accessibility and ensures consistent behavior across browsers and input methods.
