I'm working on several improvements to the Talos machine service management and configuration system and need help implementing them.

First, the service runner needs a new "starting" state that is emitted at the very beginning of each run, before the runner proceeds to waiting for conditions or preparing the service. This state should be the first in the sequence every time a service starts or restarts. Related to this, the system needs a way to notify callers that a service has reached this initial state, so that code that starts a service and then immediately tries to work with it doesn't have a race condition. The service runner also needs to be refactored so it no longer relies on a global singleton for things like waiting for dependent services — the singleton instance should be passed in explicitly, which will also make it possible to test service restart behavior in isolation.

Second, when the maintenance API is used to apply a partial configuration document, that configuration should be stored in the state store under a well-known resource identifier so other components can access it during maintenance. When maintenance mode ends, this resource should be cleaned up automatically.

Third, the configuration container needs a method to patch just the primary configuration section while automatically preserving all other document types (like secondary configuration documents). Currently this requires extracting, modifying, and reassembling the container manually each time.

Finally, configuration validation should reject etcd-specific settings when they appear in a configuration for a worker-type machine, with a clear error message indicating that such settings are only permitted on control plane machines.
