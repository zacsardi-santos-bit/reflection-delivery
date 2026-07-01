I'm working on a Merkle Mountain Range implementation in Rust and have run into several issues that need to be fixed together.

The main problem is that the storage backend currently assumes all elements it stores are a fixed, known size in bytes. This isn't always true — some data types like block headers are variable in size — so I need to add a way for the backend constructor to be told whether elements are fixed-size or variable-size. This configuration needs to be threaded through to the underlying file storage layer.

Beyond that, I've found some correctness bugs. The logical size of the tree (the total number of nodes the full un-compacted tree would have) isn't being tracked correctly — after pruning some entries and compacting the files, the reported size starts drifting from the true value. It should stay constant regardless of how many prune and compact operations have been performed.

There's also a bug with rewinding: after rewinding the tree to a previously captured state, the Merkle root hash should match what it was at that historical point, but it currently doesn't. This assertion was previously disabled in the tests because it was known to fail.

Finally, after going through a full lifecycle of pruning everything, compacting the files, and rewinding back to an empty state, the hash file and data file should reflect that state — the data file should have zero entries, and the hash file should have at most one entry — but they aren't being set correctly.

The storage layer needs to be reworked to support both fixed-size and variable-size element storage, and the pruning, compaction, and rewind logic needs to be corrected to maintain the invariants described above.
