Implement the navigation utility for the IDE and a service for managing custom widget builder windows. Ensure the navigation logic after entity deletion is clear and predictable, and manage the lifecycle of custom widget builder popup windows efficiently.

Requirements:
*   Implement `getNextEntityAfterDelete` in `app/client/src/sagas/IDESaga.tsx`.
    *   Accept parameters: `deletedId` (string) and `items` (array of `EntityItem`).
    *   Use `identifyEntityFromPath()` to determine the currently viewed entity's id.
    *   Return `{ action: RedirectAction.NA }` if `deletedId` does not match the current entity id.
    *   Return `{ action: RedirectAction.CREATE }` if `deletedId` matches the current entity id and `items` is empty.
    *   Return `{ action: RedirectAction.ITEM, payload: <first remaining item> }` if no items in the same group remain.
    *   Return `{ action: RedirectAction.ITEM, payload: <first item in same group> }` if items in the same group remain.
*   Export `RedirectAction` enum from `app/client/src/sagas/IDESaga.tsx` with values: `NA`, `CREATE`, `ITEM`.

*   Implement `Builder` class in `app/client/src/utils/CustomWidgetBuilderService.ts`.
    *   Constructor must open a new browser window and register a message event listener.
    *   Expose `builderWindow` as a public property.
    *   Use `onMessageMap` to store message listener callbacks by message type.
    *   Implement `onMessage(type, callback)` to register callbacks and return a cancel function.
    *   Implement `postMessage(message)` to send messages to the builder window.
    *   Implement `isConnected()` to check if the builder window is open.
    *   Implement `focus()` to bring the builder window into focus.
    *   Implement `close(closeWindow)` to remove event listeners and optionally close the window.

*   Implement `CustomWidgetBuilderService` as a default export in `app/client/src/utils/CustomWidgetBuilderService.ts`.
    *   Manage `Builder` instances keyed by widget ID.
    *   Implement `setBuilderFactory(factory)` to set the factory function for creating `Builder` objects.
    *   Implement `createBuilder(widgetId)` to create and store a builder using the factory.
    *   Implement `isConnected(widgetId)` to return the connection status of a builder.
    *   Implement `focus(widgetId)` to focus the builder window.
    *   Implement `closeBuilder(widgetId, closeWindow)` to close and remove the builder.
    *   Implement `getBuilder(widgetId)` to retrieve the builder instance or return undefined.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.