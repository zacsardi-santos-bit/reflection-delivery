I'm working on the peer address manager in a blockchain node. Right now, peer data is saved to disk using a text-based format, and I want to switch to a more efficient binary format. A few things need to work correctly:

First, the address manager should have a method to serialize its data to raw bytes in the new binary format, and a way to load peer data from a file that supports both the new binary format and the old text-based format — so existing nodes can migrate transparently without losing their peer lists.

Second, if the peers file doesn't exist at all (fresh install or deleted file), loading should just return an empty address manager rather than throwing an error.

Third, if the binary file contains entries with an unrecognized address type, those entries should be skipped gracefully so the rest of the peers can still be loaded. If all entries are malformed, the result should just be an empty address manager.

Finally, the module-level constants for bucket size and number of buckets should be accessible as direct imports from the address manager module, and IPv6 addresses must round-trip correctly through serialize and deserialize.
