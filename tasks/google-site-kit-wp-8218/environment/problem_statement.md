# Add Audience Management to the Analytics 4 Data Store

## Description

The Analytics 4 module in Site Kit currently has no mechanism for fetching or creating audience segments for a GA4 property through the plugin's data layer. As we build out audience-segmentation features, we need a dedicated data store that handles listing existing audiences and creating new ones, all within the same established patterns used for other analytics resources.

## Expected Behavior

- It should be possible to retrieve the list of audiences defined on a GA4 property. When the data is not yet loaded, a network request should be made automatically; when data is already present in the store, no additional request should be made.
- It should be possible to create a new audience by dispatching an action with an audience definition object. Before any request is sent, the action should validate the input:
  - Reject non-object inputs with a clear error message.
  - Reject objects that contain unrecognized property names, identifying the offending key in the error message.
  - Reject objects missing required properties, identifying the missing key in the error message.
  - Reject objects where the filter clauses property is not an array.
- After a successful creation request, the newly created audience should be reflected in the store's audience state.
- Two new constants representing valid audience filter clause types and audience filter scope values should be exported so that other parts of the codebase can reference them without using raw strings.

## Why This Matters

Without this data store module, no part of the plugin can query or manage audience segments in a consistent, testable way. Adding it unblocks audience-segmentation UI work and ensures that audience creation is guarded by proper input validation before making expensive API calls.
