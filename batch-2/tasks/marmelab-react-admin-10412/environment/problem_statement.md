## Description

React Admin forms support pre-populating fields from navigation context (either router state or URL query parameters), but this functionality is currently only available within the built-in "Create" page and is not accessible as a standalone, reusable utility. Developers building forms outside of the standard create page — for example, multi-tab forms that use sub-routes to display different sections — cannot use this pre-population capability without duplicating internal logic.

Additionally, when a developer navigates to a form with pre-population data in the URL or router state, the form should apply those values even when there is an existing record already loaded. The location-based values should override the relevant fields in the initial record.

## Expected Behavior

- A standalone hook should exist that any form can call to retrieve the record pre-population data from the current router location (either from hidden navigation state or visible URL parameters).
- The hook should support customizing which key in the navigation state or URL query string is read, rather than being locked to hardcoded key names.
- Forms should automatically pick up pre-population data from the location and apply it as field values, merging correctly with any existing record and default values.
- When navigating between tabs or sub-routes within a multi-route form, pre-populated values should remain in place — the location-based data should not be re-applied or lost on navigation.
- When both navigation state and URL query parameters contain pre-population data, navigation state should take precedence.

## Why This Matters

This makes form pre-population a first-class, reusable capability across all form contexts — not just the create page — enabling use cases like pre-filling an edit form from a workflow step, or detecting from the UI whether the current form has been pre-populated with overrides.
