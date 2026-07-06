Implement a new navigation mode for OpenSearch Dashboards that organizes links by use case. Add support for registering navigation controls at the bottom of the sidebar, create a collapsible navigation component, and update core plugins to register their links into appropriate groups.

*   Update the NavControlsService:
    *   Implement `registerLeftBottom(navControl: ChromeNavControl): void` in `src/core/public/chrome/nav_controls/nav_controls_service.ts` to register controls at the bottom-left.
    *   Implement `getLeftBottom$(): Observable<ChromeNavControl[]>` to return an observable of registered controls sorted by order.

*   Modify the Header component:
    *   Rename `currentNavgroup$` to `currentNavGroup$` in the HeaderProps interface in `src/core/public/chrome/ui/header/header.tsx`.
    *   Add `navGroupsMap$`, `navControlsLeftBottom$`, and `setCurrentNavGroup` to HeaderProps.
    *   Render `CollapsibleNavGroupEnabled` when `navGroupEnabled` is `true`.

*   Create the CollapsibleNavGroupEnabled component:
    *   Implement in `src/core/public/chrome/ui/header/collapsible_nav_group_enabled.tsx`.
    *   Accept `CollapsibleNavGroupEnabledProps` including various observables and navigation functions.
    *   Ensure it renders links based on the current navigation state and supports group navigation.

*   Develop the NavGroups component:
    *   Export from `collapsible_nav_group_enabled.tsx`.
    *   Render nav links with `nav-link-item-btn` class and `data-test-subj` attribute.
    *   Handle click events to navigate to the app.

*   Create the CollapsibleNavTop component:
    *   Implement in `src/core/public/chrome/ui/header/collapsible_nav_group_enabled_top.tsx`.
    *   Accept `CollapsibleNavTopProps`.
    *   Render elements based on navigation state, including home, back, and shrink buttons.

*   Update core plugins:
    *   In `AdvancedSettingsPlugin` (`src/plugins/advanced_settings/public/plugin.ts`), call `application.register` once in `setup()`.
    *   In `DashboardPlugin` (`src/plugins/dashboard/public/plugin.tsx`), call `chrome.navGroup.addNavLinksToGroup` five times in `setup()`.
    *   In `DiscoverPlugin` (`src/plugins/discover/public/plugin.ts`), call `chrome.navGroup.addNavLinksToGroup` five times in `setup()`.
    *   In `IndexPatternManagementPlugin` (`src/plugins/index_pattern_management/public/plugin.ts`), call `application.register` once and `chrome.navGroup.addNavLinksToGroup` five times in `setup()`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.