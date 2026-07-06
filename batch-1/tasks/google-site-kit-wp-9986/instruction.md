Migrate the consent mode setup banner to use the centralized notification system. Ensure it is registered with the notification management system and updates its component to use standard notification props. Implement logic to display the banner only when ads are connected and consent mode is not enabled.

*   Update `DEFAULT_NOTIFICATIONS` in `assets/js/googlesitekit/notifications/register-defaults.js`:
    *   Include an entry keyed by `CONSENT_MODE_SETUP_CTA_WIDGET_SLUG`.
    *   Implement `checkRequirements` as an async function:
        *   Accept `{ select, resolveSelect }` as the first argument and `viewContext` as the second.
        *   Return `true` only if `isConsentModeEnabled()` returns `false` and `isAdsConnected()` returns a truthy value.
        *   Return `false` if `isConsentModeEnabled()` returns `true`, regardless of ads connection.
        *   Return `false` if `isAdsConnected()` returns `false`, regardless of consent mode status.
        *   Use `await resolveSelect(CORE_SITE).getConsentModeSettings()` before checking `isConsentModeEnabled()`.

*   Modify `ConsentModeSetupCTAWidget` in `assets/js/components/consent-mode/ConsentModeSetupCTAWidget.js`:
    *   Export as a direct default without wrapping with `withWidgetComponentProps`.
    *   Accept `{ id, Notification }` props instead of `{ Widget, WidgetNull }`.
    *   Render with a `section` element with `id='consent-mode-setup-cta-widget'` when using `withNotificationComponentProps`.
    *   Ensure the inner widget `div` includes CSS classes:
        *   `googlesitekit-setup-cta-banner--single-column`
        *   `googlesitekit-setup-cta-banner--consent-mode-setup-cta-widget`
    *   Return `null` when the banner is dismissed or while dismissal state is loading.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.