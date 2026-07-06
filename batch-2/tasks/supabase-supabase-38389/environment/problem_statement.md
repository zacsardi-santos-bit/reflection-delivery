## Description

We need a new reusable filter bar component in our shared UI package. Right now there is no standard way for product teams to let users build structured queries against tabular data — developers have to implement custom one-off solutions every time they need filtering.

The component should combine a free-text search input with a structured filter builder. When a user clicks into the search field, they should see a list of filterable properties. Selecting a property should add a new filter condition and let the user enter or pick a value. The bar should support:

- A list of predefined options (shown as a dropdown when the user focuses the value field)
- Custom value-picker components that render directly in the filter area
- Nested filter groups that can combine conditions with logical operators
- Clean rendering where logical operators between conditions are hidden by default

The component should also ship with well-tested utility functions for all the common immutable state mutations — adding/removing conditions, updating values and operators, toggling logical operators, navigating filter group trees by path — as well as hooks that manage the UI state of the bar (popover visibility, active inputs, loading and error states for async options).

## Expected Behavior

- A search input is always visible with a clear placeholder indicating it supports both search and filtering
- Clicking into the search input opens a popover listing available properties to filter by
- Selecting a property adds a new filter condition and shows an accessible value input for that property
- Properties with a fixed list of options show those options when the value input is focused
- Properties with a custom picker component render that UI directly when the value input is focused
- Clicking anywhere outside the filter bar closes any open popover
- Pre-existing filter conditions passed via props are displayed immediately on render
- Nested filter groups are rendered recursively, showing all conditions regardless of nesting depth
- Logical operator labels are hidden by default; a prop controls whether they are shown

## Why This Matters

This provides a shared, well-tested filter bar that all product surfaces can adopt consistently, reducing duplication and making it easier to ship sophisticated filtering experiences without reinventing the wheel each time.
