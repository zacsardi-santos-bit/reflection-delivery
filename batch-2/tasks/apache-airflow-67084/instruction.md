I'm working on the Airflow navigation bar and running into a limitation with how plugin links are displayed.

*   The PluginMenus component must accept a single prop named navItems that is an array of navigation item objects, each of which may have a nav_top_level boolean field along with name, href, url_route, destination, and optional category fields.

*   When navItems is empty, the PluginMenus component must render nothing (an empty DOM element).

*   Navigation items with nav_top_level set to true ('promoted' items) must always be rendered directly on the navigation toolbar as accessible anchor links, each identified by an aria-label equal to the item's name value.

*   Navigation items without nav_top_level (or with nav_top_level not equal to true) are 'non-promoted' items. If there are 2 or more non-promoted items, they must be rendered inside a submenu and must NOT appear directly on the toolbar. The submenu trigger must be a button element with an accessible name matching 'nav.plugins'.

*   If there is exactly 1 non-promoted item (after promoted items are separated out), that single item must also be rendered directly on the toolbar — no one-item submenu should be created.

*   Promoted items always appear on the toolbar regardless of how many other plugins exist. The submenu vs. toolbar decision applies only to the count of non-promoted items.

*   The ExternalViewResponse TypeScript type (at airflow-core/src/airflow/ui/openapi-gen/requests/types.gen.ts) must include an optional nav_top_level field typed as boolean or null.

*   The Python base plugin response model (BaseUIResponse in airflow-core/src/airflow/api_fastapi/core_api/datamodels/plugins.py) must include a nav_top_level field with type bool or None and a default value of False.

*   The plugin REST API JSON response must include nav_top_level with a default value of false for all plugin items (both external view items and link/React app items).


*   Interface details: Type: Component
Name: PluginMenus
Location: airflow-core/src/airflow/ui/src/layouts/Nav/PluginMenus.tsx
Signature: PluginMenus({ navItems }: { readonly navItems: Array<NavItemResponse> }) -> JSX.Element | undefined
Description: React component that renders plugin navigation items. Items with nav_top_level === true are rendered directly as toolbar links (as accessible anchor elements with aria-label equal to the item's name). Non-promoted items (nav_top_level !== true) are placed in a submenu if there are 2 or more of them; otherwise, the single remaining non-promoted item is also rendered directly on the toolbar. The submenu trigger must be a button element with an accessible name matching "nav.plugins".

Type: TypeScript Type (field addition)
Name: ExternalViewResponse
Location: airflow-core/src/airflow/ui/openapi-gen/requests/types.gen.ts
Signature: nav_top_level?: boolean | null
Description: The ExternalViewResponse type must include an optional nav_top_level field typed as boolean | null. This field controls whether a plugin navigation item is promoted to the main toolbar.

Type: Python Model (field addition)
Name: BaseUIResponse
Location: airflow-core/src/airflow/api_fastapi/core_api/datamodels/plugins.py
Signature: nav_top_level: bool | None = False
Description: The BaseUIResponse Pydantic model must include a nav_top_level field with a default value of False. This field is serialized into the plugin API JSON response for all plugin items (ExternalViewResponse and ReactAppResponse both inherit from BaseUIResponse).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.