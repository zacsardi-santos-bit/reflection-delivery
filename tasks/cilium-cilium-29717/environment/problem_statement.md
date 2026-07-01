## Description

We need a new reusable package in the codebase that provides bit-based longest-prefix matching trie data structures. Currently there is no dedicated, efficient mechanism for prefix-based hierarchical lookups over either IP network addresses or generic unsigned integer ranges. This makes it difficult to build networking policy components that need to answer questions like "which stored prefixes cover this IP address?" or "which port ranges overlap with this range?".

## Expected Behavior

- A generic trie structure for unsigned integer keys should allow storing values indexed by a key and a bit-prefix length (similar to how network CIDRs work for integers). It should support inserting, updating, looking up the most specific match, deleting entries, iterating all entries, and querying both ancestor entries (broader prefixes that cover a given key) and descendant entries (narrower prefixes contained within a given key).
- A CIDR-specific trie should wrap the same logic for IP network prefixes, allowing lookups by IP address to find the most specific matching network, and traversal of all covering or covered prefixes in order from broadest to most specific.
- Both structures must support both IPv4 and IPv6 prefixes when used in the CIDR context.
- Ancestor traversal must visit entries from the broadest (least specific) to the most specific prefix.
- Descendant traversal must also visit entries from the broadest to most specific prefix.

## Why This Matters

Networking components that enforce policies based on IP ranges and port ranges need efficient, hierarchical lookup structures. A longest-prefix match trie lets the system quickly determine the most specific rule matching an address, and lets policy enforcement components enumerate all applicable rules at multiple levels of specificity without scanning every entry. This is a fundamental building block for scalable network policy evaluation.
