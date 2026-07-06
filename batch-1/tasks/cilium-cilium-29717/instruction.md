Implement a new package in the Cilium codebase to provide bit-based longest-prefix match trie data structures for efficient hierarchical lookups. Create a generic trie for unsigned integer keys and a specialized trie for IP network prefixes (CIDRs). Ensure both structures support operations like insertion, lookup, deletion, and traversal.

*   Create the `bitlpm` package at `pkg/container/bitlpm/`.
    *   Implement a generic unsigned-integer trie (`UintTrie`).
        *   Use `NewUintTrie[K, V]()` to construct an empty trie for unsigned integer keys.
        *   Implement `Upsert(prefix uint, key K, value V)` to insert or update entries.
        *   Implement `Lookup(key K) V` to find the most specific matching entry.
        *   Implement `Ancestors(prefix uint, key K, fn func(prefix uint, key K, v V) bool)` for ancestor traversal.
        *   Implement `Descendants(prefix uint, key K, fn func(prefix uint, key K, v V) bool)` for descendant traversal.
        *   Implement `Delete(prefix uint, key K) bool` to remove entries.
        *   Implement `ForEach(fn func(prefix uint, key K, v V) bool)` to iterate over all entries.
        *   Implement `Len() uint` to return the number of entries.
    *   Implement a CIDR/IP-prefix trie (`CIDRTrie`).
        *   Use `NewCIDRTrie[V]()` to construct an empty trie for CIDR keys.
        *   Implement `Upsert(prefix netip.Prefix, value V)` for CIDR entries.
        *   Implement `Lookup(addr netip.Addr) V` for IP address lookups.
        *   Implement `Ancestors(prefix netip.Prefix, fn func(k netip.Prefix, v V) bool)` for CIDR ancestor traversal.
        *   Implement `Descendants(prefix netip.Prefix, fn func(k netip.Prefix, v V) bool)` for CIDR descendant traversal.
    *   Define the unexported `cidrKey` type.
        *   Implement `BitValueAt(index uint) uint8` to return the bit value at a given index.
        *   Implement `CommonPrefix(other netip.Prefix) uint` to return the number of leading common bits.

*   Create the following fuzz corpus files for `FuzzUint8` tests:
    *   `pkg/container/bitlpm/testdata/fuzz/FuzzUint8/0b6e97f5d6080cd1` with content:
        *   `go test fuzz v1\n[]byte("\xff1\x93000")`
    *   `pkg/container/bitlpm/testdata/fuzz/FuzzUint8/12fa21fe54e87f30` with content:
        *   `go test fuzz v1\n[]byte("\xff1\x93100")`
    *   `pkg/container/bitlpm/testdata/fuzz/FuzzUint8/155aa7583fcdf31b` with content:
        *   `go test fuzz v1\n[]byte("0002A207 7\x8d10000000000")`

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.