I'm working on the Backstage UI package and noticed that the header navigation component has a bug with how it generates link URLs for flat tabs.

*   The HeaderNav component must render flat tab links whose href attributes include the router's configured base path. For example, a tab with href '/catalog/overview' inside a router with base path '/app' must produce an anchor with href '/app/catalog/overview'.

*   The HeaderNav component must correctly detect which flat tab is active when the current URL path (including the router base path) matches a tab's href. The matching tab's anchor must have aria-current set to 'page', and non-matching tabs must not have the aria-current attribute.

*   The HeaderNav component must render grouped tab item links (shown in dropdown menus) whose href attributes include the router's configured base path, in the same way as flat tab links.


*   Interface details: Type: Component
Name: HeaderNav
Location: packages/ui/src/components/Header/HeaderNav.tsx
Description: A header navigation component that renders either flat tab links or grouped (dropdown) tab menus. Flat tabs render as anchor links; grouped tabs render as buttons that open a dropdown menu of items. All tab and item links must include the router's configured base path in their href attributes.
Signature: HeaderNav(props: { tabs: Array<FlatTab | GroupedTab>, activeTabId?: string | null }) -> JSX.Element

Where:
- FlatTab: { id: string, label: string, href: string }
- GroupedTab: { id: string, label: string, items: Array<{ id: string, label: string, href: string }> }


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.