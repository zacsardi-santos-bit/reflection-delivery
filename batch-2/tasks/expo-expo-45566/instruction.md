I'm working on an observability package for Expo apps and need to add router-aware interactivity tracking.

*   The default export of the module file must be a Proxy object wrapping the native module. Its `configure` method must strip the `disableRouterIntegration` property from the provided options object before forwarding the remaining properties to the native module's `configure` method.

*   When `configure` is called and the router is installed (`isRouterInstalled` is true) and `disableRouterIntegration` is not set or is false, `initRouterIntegration` must be called exactly once.

*   When `configure` is called with `disableRouterIntegration: true`, `initRouterIntegration` must NOT be called, regardless of whether the router is installed.

*   When `configure` is called and the router is not installed (`isRouterInstalled` is false), `initRouterIntegration` must NOT be called.

*   The module proxy must expose `dispatchEvents()` and `setBundleDefaults(options)` methods that delegate directly to the corresponding methods on the native module.

*   Accessing any property on the module proxy that is not explicitly handled must return the corresponding property from the native module (via transparent property forwarding).

*   The `useObserve` hook must call `useObserveForRouter` (from `packages/expo-observe/src/integrations/expo-router`) on every render — including rerenders.

*   When `useObserveForRouter` returns a function, `useObserve` must return `{ markInteractive: <that function> }`. When `useObserveForRouter` returns null or a falsy value, `useObserve` must return `{ markInteractive: AppMetrics.markInteractive }`.

*   The object returned by `useObserve` must have exactly one own enumerable key: `markInteractive`.

*   The `useObserveForRouter` hook must return a function. When that function is called with an options object, it must call `AppMetrics.markInteractive` with those options merged with an additional `routeName` property set to the `pathname` from the current route info obtained via `optionalRouter.useCurrentRouteInfo()`.

*   When the current screen is not focused (i.e., `optionalRouter.useNavigation().isFocused()` returns false), calling the function returned by `useObserveForRouter` must NOT call `AppMetrics.markInteractive`.


*   Interface details: Type: Module (default export)
Name: default
Location: packages/expo-observe/src/module.ts
Signature: A Proxy object wrapping the native module obtained via `expo.requireNativeModule(...)`. Must be the default export.
Description: A Proxy over the native Expo module. Its `configure(options)` method strips the `disableRouterIntegration` key before forwarding to the native `configure`, conditionally calls `initRouterIntegration` based on router availability and the flag, and passes `dispatchEvents()` and `setBundleDefaults(options)` through to native. Arbitrary property access falls through to the native module via Reflect.get.

Type: Function
Name: configure
Location: packages/expo-observe/src/module.ts (method on the default export proxy)
Signature: configure(options: { disableRouterIntegration?: boolean; [key: string]: unknown }): void
Description: Accepts a configuration options object. Removes the `disableRouterIntegration` property before forwarding the remaining options to the native module's `configure`. If `isRouterInstalled` is true and `disableRouterIntegration` is not set (or false), calls `initRouterIntegration`. If `disableRouterIntegration` is true or the router is not installed, does not call `initRouterIntegration`.

Type: Function
Name: dispatchEvents
Location: packages/expo-observe/src/module.ts (method on the default export proxy)
Signature: dispatchEvents(): Promise<void>
Description: Delegates directly to the native module's `dispatchEvents`.

Type: Function
Name: setBundleDefaults
Location: packages/expo-observe/src/module.ts (method on the default export proxy)
Signature: setBundleDefaults(options: object): void
Description: Delegates directly to the native module's `setBundleDefaults`.

Type: Function
Name: useObserve
Location: packages/expo-observe/src/useObserve.ts
Signature: useObserve(): { markInteractive: Function }
Description: A React hook. On every render it calls `useObserveForRouter` from the expo-router integration barrel (`packages/expo-observe/src/integrations/expo-router/index.ts`). If `useObserveForRouter` returns a function, that function is used as `markInteractive`. If it returns null/falsy, `markInteractive` falls back to `AppMetrics.markInteractive`. The returned object has exactly one own key: `markInteractive`.

Type: Function
Name: useObserveForRouter
Location: packages/expo-observe/src/integrations/expo-router/useObserveForRouter.ts
Signature: useObserveForRouter(): ((...args: any[]) => Promise<void>) | null
Description: A React hook. Uses `optionalRouter.useRoute()`, `optionalRouter.useNavigation()`, and `optionalRouter.useCurrentRouteInfo()` from the router module. Returns a function that, when called with an options object, calls `AppMetrics.markInteractive` with those options merged with `routeName` set to the current route's `pathname`. If the current screen is not focused (`useNavigation().isFocused()` returns false), the returned function does NOT call `AppMetrics.markInteractive`.

Type: Re-export
Name: useObserveForRouter
Location: packages/expo-observe/src/integrations/expo-router/index.ts
Signature: export { useObserveForRouter } from './useObserveForRouter'
Description: The expo-router integration barrel must re-export `useObserveForRouter` so it is accessible as `../integrations/expo-router` from `useObserve.ts`.

Type: Module
Name: router module
Location: packages/expo-observe/src/integrations/expo-router/router.ts
Signature: exports { isRouterInstalled: boolean, optionalRouter: { useRoute, useNavigation, useCurrentRouteInfo } | undefined }
Description: Exports `isRouterInstalled` (boolean) and `optionalRouter` (object with hooks or undefined). The `optionalRouter.useCurrentRouteInfo()` returns an object with a `pathname` string property.

Type: Module
Name: init module
Location: packages/expo-observe/src/integrations/expo-router/init.ts
Signature: exports { initRouterIntegration: () => void, isInitialized: () => boolean, initListeners: () => () => void }
Description: Exports `initRouterIntegration`, `isInitialized`, and `initListeners` functions used by the module proxy to set up the router integration.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.