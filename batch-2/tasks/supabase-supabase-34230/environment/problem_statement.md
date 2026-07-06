## Description

Several tests for the Logs Explorer page are failing because the test rendering infrastructure does not properly support the URL-based state management that the page relies on. Specifically, tests that verify URL query parameters pre-populate the query input and datepicker fields are broken, along with a "field reference" test.

There are two root causes:

1. **Missing providers in test render**: The existing render helper does not wrap components with the necessary context providers — in particular, the URL state library's test adapter. Without this adapter, the URL query parameter state cannot be exercised in tests. A previous workaround manually mocked the URL state hook to always return a fixed value, which prevented tests from actually verifying URL-driven behavior.

2. **Missing navigation mocks**: The page uses navigation APIs from the application framework's newer navigation module. These are not mocked in the global test setup, causing errors when the page component is rendered in tests.

## Expected Behavior

- A shared render utility should wrap test components in all required providers, including the URL state library's test adapter, a query client provider, and a tooltip provider.
- The global test setup should include proper mocks for the framework's navigation module (router, pathname, and search params hooks).
- Tests confirming that the query string URL parameter correctly populates the query input field should pass.
- Tests confirming that the timestamp start and end URL parameters correctly populate the datepicker should pass.
- The manual override of the URL state hook in the logs test setup should be removed in favor of the proper test adapter.
- The URL state management library used by the Logs Explorer page must be upgraded to a version that provides a testing adapter.

## Why This Matters

Without these fixes, CI tests for the Logs Explorer page give false results: they either fail outright or pass for the wrong reasons (because the real URL-driven state was never being exercised). Fixing the test infrastructure ensures that URL query parameter behavior is actually verified, giving confidence that the feature works correctly.
