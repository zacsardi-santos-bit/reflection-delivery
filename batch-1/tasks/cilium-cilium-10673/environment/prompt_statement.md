I'm working on improving how Cilium handles situations where its connection to the Kubernetes API server becomes unresponsive. Right now, if the API server stops replying, Cilium's client connections just hang — there's no automatic detection or recovery.

I want to add a heartbeat function that checks whether the API server is still reachable. The logic should be: if we've successfully heard from the API server very recently (within a configurable time window), there's no need to probe — we assume things are fine. But if we haven't had a successful response in that window, we should send a probe. If the probe times out or comes back with an error, we should close all existing client connections so fresh ones can be made.

To support this, I also need to add a new metric to the existing metrics package that tracks the timestamp of the most recent *successful* API server response (distinct from the existing metric that tracks any interaction). This lets the heartbeat function distinguish between "we talked to the server recently and it worked" versus "we tried but it failed."

The heartbeat function should take the probe logic as a callback (so it's testable in isolation), a timeout duration, and one or more callbacks to call when connections need to be closed. This design keeps the function decoupled from how the actual probe is performed and from how connections are managed.
