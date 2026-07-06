I need to implement a new package in the Cilium codebase that provides bit-based longest-prefix match trie data structures for efficient hierarchical lookups. The package should live at pkg/container/bitlpm and be implemented in Go.

The package needs two kinds of tries. The first is a generic trie that works with any unsigned integer type as a key, where entries are stored under a (key, prefix-length) pair — similar to how CIDR notation works but for arbitrary unsigned integers. It needs to support inserting and updating entries, finding the most specific prefix that matches a given key, deleting entries, iterating all entries, and traversing both "ancestor" entries (those with shorter, broader prefixes that cover a given key) and "descendant" entries (those with longer, narrower prefixes covered by a given key). Ancestor and descendant traversals should both visit entries in order from the broadest prefix to the most specific.

The second is a specialized version for IP network prefixes (CIDRs) that wraps the first and works with the standard library's network prefix type. It should support inserting and updating by network prefix, looking up the most specific prefix matching a given IP address, and the same ancestor and descendant traversals.

The implementation should correctly handle both IPv4 and IPv6 addresses. The internal key type for the CIDR trie should expose bit-level operations: returning the bit value at a given index (0 or 1, from the most significant bit), and computing the number of leading bits in common between two prefixes.

I'd like these structures to be correct across a wide range of cases — sparse and dense prefix sets, single-entry tries, full-range coverage, and both in-order and reverse-order deletion.
