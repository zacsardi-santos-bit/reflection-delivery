I'm working on the Kubernetes nftables-based network proxy and I need to add the ability to load a textual snapshot of nftables rules into the simulated nftables environment used in unit tests. Right now, the only way to populate the simulated environment is to run the proxier, which makes it hard to write focused, standalone tests for specific packet routing scenarios.

I'd like to be able to initialize the simulated nftables environment from a dump string — essentially the reverse of the operation that serializes the current state to text — so I can write tests with hand-crafted static rule sets. After loading rules from a dump, serializing the state back should reproduce the original input.

There's also a bug in the simulated environment when working with IPv6 addresses that needs to be fixed, since I want to write separate tests for IPv4 and IPv6 packet routing (the simulated environment only supports one address family at a time).

The packet flow tests I need to support should cover routing to single and multiple endpoints, DROP and REJECT verdicts, traffic requiring masquerade marking, firewall source-range filtering, and NodePort routing — for both IPv4 and IPv6 scenarios.
