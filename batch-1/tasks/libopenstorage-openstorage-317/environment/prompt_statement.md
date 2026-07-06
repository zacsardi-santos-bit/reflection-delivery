I'm working on a configuration management library that stores cluster and node configuration in a distributed key-value backend. Right now, creating a manager instance requires passing a context object, which feels like unnecessary complexity — most users just want to hand over a backend handle and get to work. I'd like to simplify the constructor so it only takes the backend connection.

I also need the get/set operations to work correctly as a round-trip: whatever I write for a cluster configuration or a node configuration should come back exactly the same when I read it.

The watch/callback feature is also important to me. I want to be able to register named callback functions that get called whenever a cluster or node configuration is updated. The callbacks should receive the full updated configuration object. Right now the callback triggering isn't reliable enough to depend on in production code.

Can you help me update the configuration manager so the constructor no longer requires a context, the set/get round-trips are reliable, and registered watchers are invoked with the correct configuration data whenever a write occurs?
