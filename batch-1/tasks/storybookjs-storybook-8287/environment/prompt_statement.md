I'm running into two bugs in Storybook that surface when a decorator calls the story function more than once per render cycle.

The first issue is with knob configuration: when a story re-renders and a knob already exists in the store with the same type, any extra options I pass to the knob registration call are ignored — the panel keeps showing stale configuration from the first render. I'd expect those options to be updated each time the knob is registered with a matching type.

The second issue is that side effects inside stories are firing multiple times. If a decorator invokes the story function twice, any effects I've registered within the story run twice instead of once. This causes duplicate requests and unexpected behavior that's hard to debug. Effects should run exactly once per render cycle, no matter how many times the decorator ends up calling the story function.

Both of these need to be fixed: knob options should be kept up to date when the type matches, and side effects should be deduplicated so they only execute once per cycle.
