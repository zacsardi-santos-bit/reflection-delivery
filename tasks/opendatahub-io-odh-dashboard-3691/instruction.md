Implement support for NIM-based inference services by creating a validation utility for metrics configuration and defining constants for graph types. Fix integration application management bugs to ensure error visibility and correct error message reporting.

*   Export a new enum named `NimMetricsGraphTypes` from `frontend/src/concepts/metrics/kserve/const.ts`.
    *   Include the following string-valued members: `TIME_TO_FIRST_TOKEN`, `TIME_PER_OUTPUT_TOKEN`, `KV_CACHE`, `CURRENT_REQUESTS`, `TOKENS_COUNT`, `REQUEST_OUTCOMES`.
*   Export a new function named `isValidNimMetricsDataObject` from `frontend/src/concepts/metrics/kserve/utils.ts`.
    *   Accept a single argument of type `unknown`.
    *   Return a boolean, acting as a type guard for `NimMetricsDataObject`.
    *   Return `true` if the argument is a non-null plain object containing a key named 'config' whose value is a non-empty array.
    *   Return `false` if the argument is null, undefined, a boolean, a number, or an array.
    *   Return `false` if the argument is a non-null object that lacks a 'config' key, or if 'config' is present but is an empty array.
*   Update the integration application enablement hook in `frontend/src/utilities/useEnableApplication.tsx`.
    *   Transition to a `FAILED` state and set the error string to the exception's `message` property when the enablement API call rejects with an error.
    *   Invoke the integration app enablement service with the route and current variable values when `doEnable` is true and an internal route is provided.
    *   Invoke the integration app enablement status service with the same route.
*   Update the integration component-watching hook in `frontend/src/utilities/useWatchIntegrationComponents.tsx`.
    *   When the integration app status response for a component contains an error, update the component's spec by setting `isEnabled` to `false` and the error field to the response's error string. Ensure the component remains in the list.
    *   Set `spec.isEnabled` to `true` when the retrieved integration app status indicates the app is enabled.
    *   Re-fetch integration app enablement status and update components whenever the `forceComponentsUpdate` counter in the Redux application state changes.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.