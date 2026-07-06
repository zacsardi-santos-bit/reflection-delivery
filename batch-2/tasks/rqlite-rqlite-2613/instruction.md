I'm working on the database layer and want to improve how checkpoint operations report their results to callers.

*   The Checkpoint method on CheckpointManager must return three values: a *CheckpointMeta, an int64 byte count, and an error (changed from the previous two-value return of int64 and error).

*   On a successful checkpoint with a non-nil writer, Checkpoint must return a non-nil *CheckpointMeta where Success() returns true, a positive byte count (n > 0), and a nil error.

*   On a successful checkpoint with a nil writer (truncate-only), Checkpoint must return a non-nil *CheckpointMeta where Success() returns true, a byte count of exactly 0, and a nil error.

*   When the checkpoint is blocked by a concurrent reader, Checkpoint must return the ErrDatabaseCheckpointBusy error sentinel, a non-nil *CheckpointMeta, and a byte count of 0.

*   ErrDatabaseCheckpointBusy must satisfy the RetryableError interface, and its Retryable() method must return true.

*   The RetryableError interface must reside in the db package and define a single method: Retryable() bool.

*   CheckpointMeta must have a Success() bool method that returns true when the WAL was fully truncated (checkpoint completed successfully).

*   CheckpointMeta must implement the fmt.Stringer interface (String() string), so it can be used with %s format verbs in log and error messages.

*   Calling Checkpoint when the WAL file is empty (idempotent scenario) must succeed without error and return a non-nil *CheckpointMeta whose Success() returns true.

*   Any type or interface that previously declared Checkpoint(w io.Writer, timeout time.Duration) (int64, error) must be updated to return (*CheckpointMeta, int64, error) to remain consistent with the new signature.


*   Interface details: Type: Method
Name: Checkpoint
Location: db/checkpoint_manager.go
Signature: Checkpoint(w io.Writer, timeout time.Duration) (*CheckpointMeta, int64, error)
Description: Performs a WAL checkpoint on the underlying SQLite database. Returns a CheckpointMeta describing the outcome, the number of WAL bytes written to w, and any error. When w is nil, performs a truncate-only checkpoint (n is always 0). Returns ErrDatabaseCheckpointBusy if a concurrent reader is blocking the checkpoint.

Type: Struct
Name: CheckpointMeta
Location: db/ (package db)
Description: Holds result metadata about a completed checkpoint operation.
Signature:
  Success() bool   — returns true if the checkpoint fully completed (WAL was truncated)
  String() string  — returns a human-readable description of the checkpoint result (must support %s formatting)

Type: Interface
Name: RetryableError
Location: db/ (package db)
Description: An interface for errors that carry retryability information.
Signature:
  Retryable() bool — returns true if the error is transient and the operation may be retried

Type: Variable
Name: ErrDatabaseCheckpointBusy
Location: db/ (package db)
Description: Sentinel error returned by Checkpoint when a concurrent reader is blocking the checkpoint operation from completing. This error value must satisfy the RetryableError interface and Retryable() must return true.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.