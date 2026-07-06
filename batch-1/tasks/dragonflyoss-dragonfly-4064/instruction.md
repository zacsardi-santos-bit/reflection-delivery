Implement two utilities in the storage package: a piece-length calculator and a task-stat function. The piece-length calculator should determine an appropriate piece size based on a file's total content length, while the task-stat function should check for the existence of a task file on disk using a URL and optional metadata.

*   Export the following constants in `pkg/storage/piece.go`:
    *   `MIN_PIECE_LENGTH`: uint64, value 4 * 1024 * 1024 (4 MB).
    *   `MAX_PIECE_LENGTH`: uint64, value 64 * 1024 * 1024 (64 MB).
    *   `MAX_PIECE_COUNT`: uint64, value 500.

*   Implement `CalculatePieceLength` in `pkg/storage/piece.go`:
    *   Accepts `contentLength` as a uint64 and returns a uint64.
    *   Return `MIN_PIECE_LENGTH` if `contentLength` is 0.
    *   Compute initial piece length by dividing `contentLength` by `MAX_PIECE_COUNT` using float64 arithmetic, round up to the nearest power of two, and clamp between `MIN_PIECE_LENGTH` and `MAX_PIECE_LENGTH`.

*   Implement `StatTask` in `pkg/storage/stat_task.go`:
    *   Accepts `path` (string), `url` (string), and zero or more `StatTaskOption` values.
    *   Returns `(os.FileInfo, error)`.
    *   Return an error if `path` or `url` is empty.
    *   Return an error if neither a content length nor a piece length is provided via options.
    *   Derive piece length using `CalculatePieceLength` if only content length is provided.
    *   Generate task ID using `idgen.TaskIDV2ByURLBased(url, &pieceLength, tag, application, filteredQueryParams)`.
    *   Locate and stat the task file at: `{base}/content/tasks/{taskID[0:3]}/{taskID}`.

*   Implement functional options in `pkg/storage/stat_task.go`:
    *   `WithStatTaskContentLength(*uint64) StatTaskOption`: Sets content length.
    *   `WithStatTaskPieceLength(*uint64) StatTaskOption`: Sets piece length.
    *   `WithStatTaskTag(string) StatTaskOption`: Sets tag.
    *   `WithStatTaskApplication(string) StatTaskOption`: Sets application name.
    *   `WithStatTaskFilteredQueryParams([]string) StatTaskOption`: Sets filtered query parameters.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.