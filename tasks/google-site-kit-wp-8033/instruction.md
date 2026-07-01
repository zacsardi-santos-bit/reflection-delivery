Implement a mechanism to monitor internet connectivity and notify users when they are offline in the Site Kit plugin. Create a hook to check the connection status and a notification component to inform users of their offline status. Update the shared application data store to manage and reflect the connection state across the UI.

*   Update the CORE_UI data store:
    *   Implement `setIsOnline(value: boolean)` action to store the connection status under the key `isOnline`. The initial state should be `true`.
    *   Implement `getIsOnline(state: Object)` selector to retrieve the `isOnline` status from the state. Ensure it reads from the `isOnline` key and defaults to `true`.
    *   Rename the existing selector `getInViewResetHook` to `getInViewResetCount` to accurately reflect its function of returning `useInViewResetCount` from the state.

*   Create the `OfflineNotification` component:
    *   Export it as the default from `assets/js/components/notifications/OfflineNotification.js`.
    *   Ensure it reads the `isOnline` state using `getIsOnline()` from the CORE_UI store.
    *   Render a message matching "you are currently offline" (case-insensitive) when `isOnline` is `false`.
    *   Automatically hide the offline message when `isOnline` changes to `true`.

*   Develop the `useMonitorInternetConnection` hook:
    *   Export it as a named export from `assets/js/hooks/useMonitorInternetConnection.js`.
    *   On mount, check `navigator.onLine` and dispatch `setIsOnline` to update the CORE_UI store.
    *   Listen for `online` and `offline` events at the window level to update the `isOnline` state.
    *   When online, verify connectivity by fetching the endpoint `/google-site-kit/v1/core/site/data/health-checks`. Set `isOnline` to `true` only if the response includes a `checks` property.
    *   Poll the health-check endpoint every 120,000 milliseconds when online and every 15,000 milliseconds when offline. Initiate the first poll immediately upon mounting.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.