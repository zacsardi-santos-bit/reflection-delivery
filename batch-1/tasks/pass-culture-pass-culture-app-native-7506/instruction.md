Remove the old tab bar redesign feature flag from all relevant navigation components to ensure the updated design is always rendered without needing any flag. Ensure the reactions feature flag works independently for correct rendering.

*   Ensure the navigation tab bar components:
    *   AccessibleTabBar on web
    *   TabBar on native
    *   Render correctly with notification badge counts like '99+' when only the reactions feature flag is enabled.
*   Ensure the Header navigation component:
    *   Displays the 'Favoris' (favorites) tab correctly when only the reactions feature flag is enabled.
*   Make the updated tab bar visual design the permanent default:
    *   Remove all conditional rendering logic based on the old tab bar V2 feature flag from navigation components.
*   Remove the 'WIP_APP_V2_TAB_BAR' entry:
    *   Delete from the RemoteStoreFeatureFlags enum in `src/libs/firebase/firestore/types.ts`.
    *   Eliminate all usages of this flag in navigation components.
*   Update the following components to no longer import or check `RemoteStoreFeatureFlags.WIP_APP_V2_TAB_BAR`:
    *   `src/features/navigation/RootNavigator/Header/AccessibleTabBar.web.tsx`
    *   `src/features/navigation/RootNavigator/Header/Nav.tsx`
    *   `src/features/navigation/TabBar/TabBar.tsx`

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.