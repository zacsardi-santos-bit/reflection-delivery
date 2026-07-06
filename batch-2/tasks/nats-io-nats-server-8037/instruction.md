I'm seeing a problem in the file-based message store where a transient data corruption event permanently disables all further writes to a stream.

*   A new function `isReadErr(err error) bool` must be added in `server/filestore.go`. It must return `true` for the following error values/types: `errNoCache`, `errDeletedMsg`, `errPartialCache`, `errCorruptState`, `errPriorState`, and any instance of `errBadMsg` (matched via `errors.As`). It must return `false` for any other error, including `io.ErrShortWrite`.

*   The `setWriteErr` method on `fileStore` must NOT set `fs.werr` when called with any error for which `isReadErr` returns `true`. When called with a genuine write error (e.g. `io.ErrShortWrite`), it must still set `fs.werr` as before.

*   During a `StoreMsg` call, if a read error (as classified by `isReadErr`) is encountered and returned, neither `mb.werr` on the message block nor `fs.werr` on the file store must be set. The error must still be returned to the caller, and `isReadErr` applied to that error must return `true`.

*   After a `StoreMsg` call returns a read error, subsequent `StoreMsg` calls must succeed normally (writes must not be permanently disabled by the transient read error).

*   If `mb.werr` on a message block is set to a genuine write error (e.g. `io.ErrShortWrite`), a subsequent `StoreMsg` call must return that error, and both `mb.werr` and `fs.werr` must reflect that error.

*   The `generatePerSubjectInfo` method on `msgBlock` must, upon returning any error other than `errNoCache`, set `mb.fss` to `nil` (so callers cannot operate on incomplete per-subject state) and also clear `mb.cache` to `nil` (so that the next retry reloads from disk rather than re-using corrupt in-memory data).


*   Interface details: Type: Function
Name: isReadErr
Location: server/filestore.go
Signature: isReadErr(err error) bool
Description: Reports whether the given error originated from reading or interpreting existing on-disk data rather than from a failed write. Returns true for errNoCache, errDeletedMsg, errPartialCache, errCorruptState, errPriorState, and any errBadMsg instance (matched via errors.As). Returns false for all other errors (e.g. io.ErrShortWrite).

Type: Method
Name: setWriteErr
Location: server/filestore.go
Signature: (fs *fileStore) setWriteErr(err error)
Description: Records a write error on the file store. Must be modified so that when err satisfies isReadErr, fs.werr is NOT set (the error is logged/ignored but does not disable writes). For all other errors, existing behavior applies: fs.werr is set and further writes are blocked.

Type: Method
Name: generatePerSubjectInfo
Location: server/filestore.go
Signature: (mb *msgBlock) generatePerSubjectInfo() error
Description: Rebuilds the per-subject message index for a message block. Must be modified so that on any error return other than errNoCache, mb.fss is set to nil and mb.cache is cleared to nil. This prevents callers from using a partially-built index and ensures the next retry reloads from disk.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.