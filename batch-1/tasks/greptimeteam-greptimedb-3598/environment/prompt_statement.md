I'm working on the mito2 storage engine and need to add parallel scan support for append-mode regions. Right now the engine only has one scan strategy and it's used for everything, but for append-mode tables there's no reason to enforce ordering — the data just needs to come back, not in any particular sequence. I'd like the engine to automatically pick an unordered parallel scan path for append-mode regions when parallelism is configured, scanning multiple sources at the same time rather than one after another.

I also need a way to directly access the underlying scan object from the engine rather than always getting back a fully resolved scanner. There are cases where I want to explicitly request a sequential scan from the intermediate scan object rather than going through the automatic dispatch.

There's also a bug in the region reopen utility: it always reopens a region with empty options, which means any configuration the region was created with — like append mode or compaction settings — is silently lost. The utility needs to accept the original region options and pass them through so the region comes back up with the same configuration.

Finally, the scanner should correctly count the number of memtables it's going to scan, regardless of which scan strategy is actually being used underneath.
