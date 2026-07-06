I'm working on the Site Kit plugin and need to add internet connection monitoring with an offline notification. Right now the plugin has no awareness of whether the user is online or offline — if connectivity drops, nothing happens in the UI.

I need a hook that monitors the user's connection by checking the browser's online status and verifying it against a server health-check endpoint. The hook should poll periodically — more frequently when offline (every 15 seconds) and less frequently when online (every 2 minutes). It should also react immediately to browser online/offline events. When online status is confirmed via the health-check endpoint, it should update the shared application data store. When offline, it should also update the store immediately.

I also need a notification component that reads the online/offline state from the shared data store and displays a banner telling the user they are currently offline. The banner should disappear automatically when connectivity is restored.

On the data store side, I need to add an action that sets the online/offline status, a selector that retrieves it, and fix an incorrectly named existing selector — there's a selector currently named for "hook" that should instead be named for "count" to reflect what it actually returns.

The online/offline state in the store should default to online (true), and the state key used internally must be consistent between the action that writes it and the selector that reads it.
