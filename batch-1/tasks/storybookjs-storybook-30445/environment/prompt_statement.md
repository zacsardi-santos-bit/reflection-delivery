I'm building a new shared state management system for Storybook that can synchronize state across the different isolated environments (like the manager and preview frames) using the existing channel infrastructure.

The idea is to have a store with a leader/follower model: one environment creates the "leader" which owns the authoritative state, and any other environment can create a "follower" that automatically synchronizes with it. Both leaders and followers should be able to update the state and send custom events, with changes propagating to everyone through the channel.

I need the store to handle cases like: being created before the channel is available (queuing up until it's ready), detecting when multiple leaders accidentally exist for the same store id (flagging both as errored with a useful log message), followers that can't find a leader (rejecting the readiness promise with a clear error), and preventing operations on a store that isn't ready yet (throwing descriptive errors that include diagnostic context).

The store should also come with a hook for manager-side components, so they can subscribe to a store's state and re-render when it changes. The hook should support a selector so that only the relevant slice of state triggers a re-render.

The whole thing should live at a specific path under the shared utilities of the core package, and there should be a manual mock for the internal instance registry that tests can use.
