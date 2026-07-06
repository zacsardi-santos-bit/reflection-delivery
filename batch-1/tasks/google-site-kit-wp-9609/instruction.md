Update the notification system tests to ensure they accurately reflect production conditions and catch regressions effectively. Implement a test utility function to facilitate the registration of notifications and modify existing tests to use the actual notification display pipeline.

*   Export `DEFAULT_NOTIFICATIONS` as a named constant from `assets/js/googlesitekit/notifications/register-defaults.js`.
    *   Ensure it is a plain object with keys as notification ID strings and values as notification configuration objects.
    *   Include at least 'authentication-error', 'authentication-error-gte', 'gathering-data-notification', and 'top-earning-pages-success-notification'.

*   Update `registerDefaults` in `register-defaults.js`:
    *   Iterate over `DEFAULT_NOTIFICATIONS` to register each notification instead of individual registrations.

*   Implement `provideNotifications` in `tests/js/utils.js` and re-export it from `tests/js/test-utils.js`.
    *   Function signature: `provideNotifications(registry, extraData, overwrite = false)`.
    *   When `overwrite` is false, start with `DEFAULT_NOTIFICATIONS['gathering-data-notification']` and merge `extraData`.
    *   When `overwrite` is true, use only `extraData`.
    *   Register each notification using `coreNotifications.createNotifications(registry).registerNotification(notificationID, notifications[notificationID])`.

*   Modify notification logic and tests:
    *   Ensure 'authentication-error-gte' appears only when exactly one unsatisfied scope (tagmanager readonly) exists and the Analytics-4 module is connected.
    *   Ensure `ErrorNotifications` renders nothing when the user is not authenticated, regardless of unsatisfied scopes.
    *   Ensure `ErrorNotifications` renders 'Site Kit can't access necessary data' when the user is authenticated with unsatisfied scopes and 'authentication-error' is registered.
    *   Ensure `ErrorNotifications` renders 'Site Kit needs additional permissions to detect updates to tags on your site' when only the tagmanager readonly scope is unsatisfied and both 'authentication-error' and 'authentication-error-gte' are registered.
    *   Ensure `GA4AdSenseLinkedNotification` does not render under conditions such as inactive AdSense module, unlinked accounts, existing report data, dismissed notification, or incorrect view context.
    *   Ensure `GA4AdSenseLinkedNotification` renders 'Your AdSense and Analytics accounts are linked' when conditions are met: active and connected modules, linked accounts, no report data, and notification not dismissed.
    *   Ensure no data fetch from the analytics-4 report data endpoint if the notification is dismissed or the view context is incorrect.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.