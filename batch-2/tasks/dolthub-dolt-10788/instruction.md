I'm seeing a correctness issue in the table file pruning logic.

*   The newFileTableReader function must accept a refCounter value as a parameter positioned between the mmapArchiveIndexes bool parameter and the stats *Stats parameter.

*   The nomsFileTableReader function must accept a refCounter value as a parameter positioned between the chunkCount uint32 parameter and the MemoryQuotaProvider parameter.

*   A noopRefCounter struct type must exist in the nbs package that satisfies the refCounter interface with no-op addRef and decRef operations.

*   When PruneTableFiles is called, any table file that was opened via the persister's Open method and has not yet been closed must be preserved on disk, even if it is not included in the keeper hash set.

*   Table files that are not currently open AND are not in the keeper hash set must still be deleted by PruneTableFiles.

*   The Open method on the file table persister must track the opened file so that PruneTableFiles can identify and protect it from deletion.


*   Interface details: Type: Function
Name: newFileTableReader
Location: go/store/nbs/file_table_reader.go
Signature: newFileTableReader(ctx context.Context, dir string, h hash.Hash, chunkCount uint32, q MemoryQuotaProvider, mmapArchiveIndexes bool, refs refCounter, stats *Stats) (chunkSource, error)
Description: Opens a file-backed table reader. The refs refCounter parameter is added between mmapArchiveIndexes and stats to support reference-counting of open files.

Type: Function
Name: nomsFileTableReader
Location: go/store/nbs/file_table_reader.go
Signature: nomsFileTableReader(ctx context.Context, path string, h hash.Hash, chunkCount uint32, refs refCounter, q MemoryQuotaProvider) (chunkSource, error)
Description: Opens a noms-format file table reader. The refs refCounter parameter is inserted between chunkCount and q.

Type: Struct (interface implementation)
Name: noopRefCounter
Location: go/store/nbs/file_table_persister.go
Signature: noopRefCounter struct{}; implements refCounter interface with no-op addRef() and decRef() methods
Description: A no-op implementation of the refCounter interface, used when reference counting is not needed (e.g., in tests or one-off reads).

Type: Interface
Name: refCounter
Location: go/store/nbs/file_table_persister.go
Signature: interface { addRef(); decRef() }
Description: Interface for tracking reference counts on open table files. Implementations allow the persister to know when a file is still in use.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.