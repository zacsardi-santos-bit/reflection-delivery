Hey, got a cluster of related fixes I want to knock out in the Talos service and config layers, all kind of tangled together but here goes.

First thing, the service runner in the machined service lifecycle needs a new "starting" state that gets emitted right at the top of each run, before it waits on conditions or preps the service. It should always be the first state in the sequence every time a service starts or restarts. And I want a way to notify callers that a service has hit that initial state so that code which starts a service and then immediately turns around to work with it (or stop it) doesn't race. Oh, also, the runner still leans on a global singleton for stuff like waiting on dependent services and I want that passed in explicitly instead, which as a bonus lets me test restart behavior in isolation without the global getting in the way.

Second, the maintenance API. When someone applies a partial config document through it, that config should get stored in the state store under a well-known resource id so other components can read it while maintenance is running, and then when maintenance mode ends that resource needs to get cleaned up automatically instead of lingering.

Third, the config container needs a method to patch just the primary config section while automatically preserving everything else in there, the secondary documents like SideroLink config and whatnot. Right now I keep writing the same boilerplate to extract, modify, and reassemble the container by hand and it's easy to accidentally drop one of those secondary docs.

Last one, config validation. Right now etcd-specific settings sail right through when they're set on a worker-type machine, no complaint. That's wrong, etcd only makes sense on control plane nodes, so validation should reject it outright with a clear error saying those settings are only permitted on control plane machines.

Net effect I'm after is better visibility into what services are doing, config that actually gets cleaned up after maintenance, less copy-paste when patching containers, and validation that catches the worker+etcd misconfig early.
