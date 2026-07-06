I'm trying to add a custom view to a collection in the admin panel — something like a grid or map view that lives at its own URL under the collection.

*   The isPathMatchingRoute function must support non-exact (prefix) matching: when exact is not true, it must return true if currentRoute starts with the resolved view path AND the remaining portion of currentRoute is either empty or begins with '/' (i.e., matching occurs at a segment boundary). For example, '/dashboard/settings' and '/dashboard/settings/advanced/debug' must both match the path '/dashboard', but '/dashboard-extra' must not.

*   The isPathMatchingRoute function must handle the root path '/' correctly in non-exact mode: '/' must match only itself and must NOT match paths like '/login', '/collections/posts', or '/collections/posts/123'.

*   The isPathMatchingRoute function must return false when path is undefined or an empty string, regardless of currentRoute.

*   The isPathMatchingRoute function must be case-insensitive by default. When the sensitive option is true, matching must be case-sensitive.

*   The isPathMatchingRoute function must tolerate trailing slash mismatches by default. When the strict option is true, a trailing slash difference between currentRoute and path must result in no match.

*   The getCustomCollectionViewByRoute function must strip the adminRoute prefix from currentRoute before matching. When adminRoute is '/', no stripping should be applied. When adminRoute is a non-root prefix (e.g. '/admin' or '/cms'), that prefix must be removed from currentRoute before comparing against baseRoute + view.path.

*   The getCustomCollectionViewByRoute function must skip built-in view keys 'edit' and 'list' when iterating over the views object, so these are never matched as custom views.

*   The getCustomCollectionViewByRoute function must skip any view entry that does not have both a 'path' property (as a string) and a 'Component' property. Views missing either property must not be matched.

*   The getCustomCollectionViewByRoute function must return { viewKey: string, view: { payloadComponent: <the Component value> } } for a matching view, and { viewKey: null, view: {} } (with payloadComponent undefined) when no match is found or when views is undefined.

*   The warnOnInvalidCustomViews function must call console.warn exactly once for each non-built-in custom view that is missing a 'path' property. The warning message must contain the view key surrounded by double quotes (e.g. "grid") and the collection slug surrounded by double quotes (e.g. "my-collection").

*   The warnOnInvalidCustomViews function must call console.warn exactly once for each non-built-in custom view that has a 'path' but is missing a 'Component'. The warning message must contain the view key in double quotes, the collection slug in double quotes, and the literal string "Component" in double quotes.

*   The warnOnInvalidCustomViews function must not issue any warnings for the built-in view keys 'edit' and 'list', even if they lack a path or Component.

*   The warnOnInvalidCustomViews function must do nothing (no warnings) when the collection has no views configured (views is undefined or absent).

*   When a collection has multiple custom views both missing required properties, warnOnInvalidCustomViews must emit one warning per invalid view (not a single combined warning).

*   Built-in system routes (such as folder browsing routes) must take precedence over custom collection views registered at the same path. If a custom view's path conflicts with a built-in route, the built-in view must be rendered instead of the custom view.


*   Interface details: Type: Function
Name: isPathMatchingRoute
Location: packages/next/src/views/Root/isPathMatchingRoute.ts
Signature: isPathMatchingRoute({ currentRoute: string, path: string | undefined, exact?: boolean, sensitive?: boolean, strict?: boolean }): boolean
Description: Determines whether a given current route matches a configured view path. When path is undefined or empty, returns false. When exact is true, the route must match the full path pattern (supports parameterized segments like :id). When exact is false or omitted, the route may be the path itself or any sub-path at a segment boundary (e.g., /dashboard/settings matches /dashboard, but /dashboard-extra does not). Matching is case-insensitive by default; set sensitive: true for case-sensitive matching. Trailing slashes are accepted by default; set strict: true to require an exact trailing-slash match. The root path '/' in non-exact mode must NOT match arbitrary sub-paths like /login or /collections/posts — it only matches itself.

Type: Function
Name: getCustomCollectionViewByRoute
Location: packages/next/src/views/Root/getCustomCollectionViewByRoute.ts
Signature: getCustomCollectionViewByRoute({ adminRoute: string, baseRoute: string, currentRoute: string, views: SanitizedCollectionConfig['admin']['components']['views'] | undefined }): { view: { payloadComponent?: PayloadComponent<AdminViewServerProps> }, viewKey: string | null }
Description: Finds the matching custom collection view for the current URL. Strips the adminRoute prefix from currentRoute (when adminRoute is '/', no stripping is needed). Iterates over the views object, skipping built-in keys 'edit' and 'list'. For each remaining view key, checks whether the view has both a 'path' and a 'Component' property, then calls isPathMatchingRoute with the full path (baseRoute + view.path) and the view's exact/sensitive/strict settings. Returns the first matching { viewKey, view: { payloadComponent } } or { viewKey: null, view: {} } (with payloadComponent undefined) when there is no match or views is undefined.

Type: Function
Name: warnOnInvalidCustomViews
Location: packages/payload/src/collections/config/sanitize.ts
Signature: warnOnInvalidCustomViews(collection: CollectionConfig): void
Description: Validates custom collection views at startup and logs warnings for misconfigured entries. Iterates over collection.admin?.components?.views, skipping built-in keys 'edit' and 'list'. For each non-built-in view that has a 'Component' but is missing 'path': calls console.warn with a message that contains the view key surrounded by double quotes (e.g. "grid") and the collection slug surrounded by double quotes (e.g. "my-collection"). For each non-built-in view that has a 'path' but is missing 'Component': calls console.warn with a message that contains the view key in double quotes, the collection slug in double quotes, and the word Component surrounded by double quotes (e.g. "Component"). Issues one warning per invalid view. Does nothing when views is undefined.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.