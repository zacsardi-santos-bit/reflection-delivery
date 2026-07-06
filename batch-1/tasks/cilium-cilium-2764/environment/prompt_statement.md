I'm working on the xDS resource management layer for a network proxy system and I need to add acknowledgment tracking to resource updates. Right now, when I push a configuration change to proxy nodes via the discovery protocol, I have no way to know when a node has actually applied that change. I need a mechanism where I can attach a callback to an insert, update, or delete operation that fires automatically once all the specified nodes have confirmed they've applied the relevant version.

For insertions and updates, the acknowledgment should require each node to confirm both the right version and the right resource name. For deletions, since the deleted resource won't appear in the node's ACK by name, we should only require version confirmation from each node. If a node sends a more recent version acknowledgment than we were waiting for, that should count too. When multiple nodes are listed, the callback should only fire once every one of them has acknowledged.

I also need the server configuration to be updated so that each resource type carries both the resource data source and the acknowledgment observer together, rather than only the source. This way, when ACKs arrive on the discovery protocol stream, they can be forwarded to the right observer.

Additionally, I need a utility function that can parse an Istio-style proxy node identifier and extract the node's IP address from it. The format is four tilde-separated parts, where the second part is the IP address. The function should return an error if the input is malformed, has the wrong number of parts, or contains an invalid IP address.

Finally, the cache's upsert, delete, and transaction methods need a new boolean parameter that controls whether updates are forced even when the resource value hasn't changed.
