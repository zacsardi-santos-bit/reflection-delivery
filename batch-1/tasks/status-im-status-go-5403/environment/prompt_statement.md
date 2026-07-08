I'm building out a connector service on the node so external clients like dApps can route raw RPC calls through us, and right now there's just no dedicated service layer for that, no clean way to proxy calls into the underlying RPC infra. I want this to live as its own service package following the same patterns the other node services already use.

First thing, it needs to be togglable via a boolean flag in the node config, enabled or disabled, and that setting has to persist to the database alongside all the other node config fields so it saves and loads correctly with everything else. Don't special-case it, just make it round-trip through the DB like the rest of the config.

The service itself should implement the standard lifecycle interface, so start and stop both need to succeed cleanly without errors. Also it should expose no peer-to-peer protocols at all, that list stays empty.

On the RPC side I want it to register a versioned RPC namespace through the node's RPC layer. Through that namespace callers can submit raw RPC requests, and valid Ethereum methods should forward through and come back successfully, while requests for methods that don't exist come back with a response indicating the method isn't available or doesn't exist. That's really the core of it, the raw call forwarding.

So basically: new connector service package with its config boolean, DB persistence of that enabled state, the start/stop lifecycle methods, the versioned namespace registration, and the raw RPC call forwarding. This gives dApp connectors a proper standard, versioned entry point into the node's RPC capabilities while respecting the same enable/disable configurability the other services have.
