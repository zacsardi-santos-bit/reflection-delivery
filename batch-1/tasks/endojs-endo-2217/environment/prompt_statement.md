I'm working on the peer connection layer of a distributed daemon and running into two related problems.

The first is a race condition when two peers try to connect to each other at the same time. Right now there's no arbitration — both connections end up open, and neither knows which one to use. I need a connection manager that can handle simultaneous inbound and outbound connection attempts for the same peer, pick exactly one winner based on the peers' identities (using lexicographic comparison of their ID strings), and properly entangle and propagate cancellation so that tearing down any one connection in the group cascades to all related ones. The manager should also support re-establishing a connection cleanly after a previous one is fully torn down.

The second problem is that when I send a capability object to a remote peer and it comes back — for example, passed as an argument to a remote method that simply returns it — my local code doesn't recognize it as the same object. I need round-trip identity to be preserved so that a remotable object sent over the network and returned is strictly identical to the original.

There's also a usability issue: right now both peers need to explicitly introduce themselves to each other before communication works. It should be sufficient for only one side to perform the introduction — the other peer should be able to connect back without a separate explicit step.

I need a new module that implements the connection arbitration logic described above, and the broader peer communication infrastructure needs to be updated so that identity is preserved on round-trips and a single peer introduction is enough for bidirectional connectivity.
