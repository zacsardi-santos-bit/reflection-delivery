Refactor the Profile Settings page by splitting it into four separate components, each responsible for its own data fetching and rendering. Implement a preference-fetching hook that allows components to select only the specific preference value they need.

*   Implement the `MaskSensitiveData` component:
    *   Render a heading "Mask Sensitive Data" and a checkbox.
    *   Fetch the `maskSensitiveData` preference from the API.
    *   When `maskSensitiveData` is true, check the checkbox and display "Sensitive data is masked".
    *   When false or undefined, uncheck the checkbox and display "Sensitive data is visible".
    *   Location: `packages/manager/src/features/Profile/Settings/MaskSensitiveData.tsx`
    *   Signature: `export const MaskSensitiveData = () => JSX.Element`

*   Implement the `Notifications` component:
    *   Render a heading "Notifications" and a checkbox.
    *   Fetch the `email_notifications` preference from the API.
    *   When `email_notifications` is true, check the checkbox and display "Email alerts for account activity are enabled".
    *   When false, uncheck the checkbox and display "Email alerts for account activity are disabled".
    *   Location: `packages/manager/src/features/Profile/Settings/Notifications.tsx`
    *   Signature: `export const Notifications = () => JSX.Element`

*   Implement the `Theme` component:
    *   Render a heading "Theme" and radio inputs labeled "System", "Light", and "Dark".
    *   Fetch the `theme` preference from the API.
    *   Default to "System" when no theme preference is stored or when `theme` is "system".
    *   Check "Light" when `theme` is "light" and "Dark" when `theme` is "dark".
    *   Location: `packages/manager/src/features/Profile/Settings/Theme.tsx`
    *   Signature: `export const Theme = () => JSX.Element`

*   Implement the `TypeToConfirm` component:
    *   Render a heading "Type-to-Confirm" and a checkbox.
    *   Fetch the `type_to_confirm` preference from the API.
    *   When undefined or true, check the checkbox and display "Type-to-confirm is enabled".
    *   When false, uncheck the checkbox and display "Type-to-confirm is disabled".
    *   Location: `packages/manager/src/features/Profile/Settings/TypeToConfirm.tsx`
    *   Signature: `export const TypeToConfirm = () => JSX.Element`

*   Update the `MaskableText` component:
    *   Ensure the preferences hook returns the `maskSensitiveData` boolean directly.

*   Update the `getInitialValuesFromUserPreferences` function:
    *   Modify it to accept `ManagerPreferences['sortKeys']` directly.
    *   Location: `packages/manager/src/components/OrderBy.tsx`

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.