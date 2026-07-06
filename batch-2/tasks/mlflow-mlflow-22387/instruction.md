I'm working on a multi-workspace feature in our application and running into a few issues I'd like to fix together.

*   The extractWorkspaceFromSearchParams function must return null for workspace names that are a single character long (minimum valid length is 2 characters).

*   The extractWorkspaceFromSearchParams function must return null for workspace names that are 64 or more characters long (maximum valid length is 63 characters).

*   The prefixRouteWithWorkspace function must append '?workspace=<name>' to a route even when the workspace feature flag is disabled, provided an active workspace is already set in memory. When the feature flag is disabled and no active workspace is set, the route must be returned unchanged.

*   The appendWorkspaceSearchParams function must append '?workspace=<name>' to a pathname even when the workspace feature flag is disabled, provided an active workspace is already set in memory. When the feature flag is disabled and no active workspace is set, the pathname must be returned unchanged.

*   The ErrorView component must accept a 'disableWorkspacePrefixOnFallback' boolean prop. When this prop is true, the fallback home page link href must equal exactly the fallbackHomePageReactRoute value with no workspace query parameter prepended.

*   The WorkspaceSelector component must show a 'Go to workspace "<name>"' option in the search dropdown when the typed input, after trimming surrounding whitespace, is a valid workspace name that does not exactly match any listed workspace. When this option is activated (via Enter key or click), window.location.hash must be set to '#/experiments?workspace=<trimmedName>' and window.location.reload must be called.

*   The WorkspaceSelector component must trim surrounding whitespace from the typed search input before displaying the 'Go to workspace' action and before performing navigation. A trimmed name that exactly matches a listed workspace must not show the action.

*   The WorkspaceSelector component must still display the 'Go to workspace "<name>"' option and allow navigation when the workspace list fails to load (showing an error message alongside the action).

*   The WorkspaceSelector component must not display the 'Go to workspace' action and must not navigate when the typed workspace name is invalid (e.g., contains spaces).

*   A WorkspaceRouterSync component must be exported from MlflowRouter.tsx. It must accept a 'workspacesEnabled' boolean prop. When workspacesEnabled is true and the URL contains a workspace query parameter, the component must call setActiveWorkspace with the extracted workspace name and must not trigger any navigation redirect.

*   When WorkspaceRouterSync is rendered with workspacesEnabled=true and the workspace list is loading, is in an error state, or does not include the URL workspace, the component must still call setActiveWorkspace with the URL workspace name and must not redirect.

*   When WorkspaceRouterSync is rendered with workspacesEnabled=false and there is a currently active workspace in memory, the component must call setActiveWorkspace(null) and setLastUsedWorkspace(null) to clear the stale workspace state.

*   The HomePage component must detect workspace-not-found errors: when the experiment list API returns a resource-not-found error whose message contains 'not found' in the context of the requested workspace (e.g., "Workspace 'name' not found" or "Workspace 'name' not found in workspace store"), the page must render a heading 'Page Not Found', display the text 'Workspace "<name>" was not found, go back to the home page.', and render a link with href '/' (no workspace prefix).

*   When HomePage detects a workspace-not-found error, it must clear both the active workspace and the last-used workspace state (setting both to null).

*   HomePage must NOT render the workspace-not-found error page for generic server errors or for resource-not-found errors whose messages are unrelated to the current workspace (e.g., 'Experiment not found').


*   Interface details: Type: Component
Name: WorkspaceRouterSync
Location: mlflow/server/js/src/MlflowRouter.tsx
Description: React component that synchronizes workspace state from the URL with the application's active workspace. Exported as a named export.
Signature: WorkspaceRouterSync({ workspacesEnabled: boolean }): React.ReactElement | null

---

Type: Function
Name: extractWorkspaceFromSearchParams
Location: mlflow/server/js/src/workspaces/utils/WorkspaceUtils.ts
Signature: extractWorkspaceFromSearchParams(searchParams: string | URLSearchParams): string | null
Description: Extracts and validates a workspace name from URL search parameters. Must return null for names shorter than 2 characters or 64 characters or longer (in addition to existing validation rules for uppercase, spaces, consecutive hyphens, etc.).

---

Type: Function
Name: prefixRouteWithWorkspace
Location: mlflow/server/js/src/workspaces/utils/WorkspaceUtils.ts
Signature: prefixRouteWithWorkspace(route: string): string
Description: Prepends workspace query parameter to a route string. Must append '?workspace=<name>' even when the workspace feature flag is disabled, as long as an active workspace is set in memory. When the feature flag is disabled and no workspace is in memory, returns the route unchanged.

---

Type: Function
Name: appendWorkspaceSearchParams
Location: mlflow/server/js/src/workspaces/utils/WorkspaceUtils.ts
Signature: appendWorkspaceSearchParams(pathname: string): string
Description: Appends workspace query parameter to a pathname. Must append '?workspace=<name>' even when the workspace feature flag is disabled, as long as an active workspace is set in memory. When the feature flag is disabled and no workspace is in memory, returns the pathname unchanged.

---

Type: Component
Name: ErrorView
Location: mlflow/server/js/src/common/components/ErrorView.tsx
Description: Error display component. Gains a new optional boolean prop 'disableWorkspacePrefixOnFallback'. When this prop is true, the fallback home page link href must equal exactly the fallbackHomePageReactRoute value without any workspace query parameter.
Signature: ErrorView({ statusCode: number, fallbackHomePageReactRoute?: string, subMessage?: string, disableWorkspacePrefixOnFallback?: boolean, ...rest }): React.ReactElement

---

Type: Component
Name: WorkspaceSelector
Location: mlflow/server/js/src/workspaces/components/WorkspaceSelector.tsx
Description: Workspace picker dropdown. Gains a "Go to workspace" typed-entry feature. When the search input contains a valid workspace name (trimmed) that is not an exact match of any listed workspace, a 'Go to workspace "<name>"' option is shown. Selecting it (via Enter key or click) sets window.location.hash to '#/experiments?workspace=<name>' and calls window.location.reload(). Surrounding whitespace is trimmed before validation and display. The feature remains available even when the workspace list fails to load. Invalid names (e.g., containing spaces) must not show the action.

---

Type: Component
Name: HomePage
Location: mlflow/server/js/src/home/HomePage.tsx
Description: Application home page component. Must detect workspace-not-found API errors — when the experiment list request returns a resource-not-found error whose message matches the pattern for a missing workspace (e.g., "Workspace 'name' not found" or "Workspace 'name' not found in workspace store"). On detection: renders a heading reading "Page Not Found"; renders the message 'Workspace "<name>" was not found, go back to the home page.' (using double quotes around the name); renders a link with href '/'; and clears both getActiveWorkspace and getLastUsedWorkspace to null. Generic server errors and unrelated resource-not-found errors must not trigger this behavior.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.