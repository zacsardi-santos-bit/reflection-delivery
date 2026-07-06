## Description

The Absolute integration needs to be updated to work with the provider's third-generation API. Currently the integration communicates with v2 endpoints using an older request scheme. The newer API version changes endpoint paths, payload field names, and the structure of responses for several operations.

## Expected Behavior

- All freeze message operations (list, create, update, delete) should use the updated API paths.
- The device freeze payload should use updated field names required by the new API contract (the scheduled date field and the request name field have been renamed, and an obsolete field should be removed). The case-sensitive format of freeze type values should be accepted as-is and forwarded to the API.
- The device unenrollment operation should be redesigned for the new API: it should perform a multi-step flow that first submits the unenroll request, then retrieves a summary of the overall request status (including counts of devices in various states such as pending, processing, completed, canceled, and failed), and finally retrieves the list of per-device action records. The combined result should be returned as a single structured output object.
- A new operation should be available for removing an existing device freeze request, producing a confirmation message that includes the affected device IDs.
- Pagination should support token-based navigation (a "next page" token alongside a page size limit) rather than the older offset-based approach.
- The request preparation logic should be updated to accept a request body parameter, wrapping non-empty bodies in a data envelope before signing.
- The custom device field listing operation should look up the device identifier from the command arguments rather than from the API response.

## Why This Matters

Without these updates, all device management operations that rely on the newer API will fail. Security teams using this integration to remotely freeze, unfreeze, and unenroll devices will be unable to perform those actions, and event fetching will also break.
