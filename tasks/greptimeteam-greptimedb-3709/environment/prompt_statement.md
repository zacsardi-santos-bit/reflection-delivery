I'm working on cleaning up some rough edges in the region management layer of our storage engine. There are a few related issues I'd like to address at once.

First, when a write is attempted on a region that isn't accepting writes — either because it's a follower that was opened in read-only mode, or because it was gracefully set to read-only — we currently report that the region is "read-only." But that's not quite right; the better signal is that the region is "not ready" for writes. These are semantically different and the distinction matters for callers deciding how to respond to the error.

Second, the way we access the current manifest on a region is unnecessarily cumbersome. Right now, code has to acquire a lock on the manifest manager and hold it while reading the manifest. I'd like to replace this with a manifest context field on the region that exposes an async method to get the current manifest — no explicit lock handling required.

Third, the manifest manager's shutdown method currently returns a result that callers must handle, but the operation is effectively infallible. I'd like to make it return nothing so all the call sites can drop the unnecessary error handling.

Can you make these changes so that writes to non-ready regions return the correct "not ready" error code, regions expose a manifest context with a simple async manifest accessor, and the manifest manager shutdown is unconditionally infallible?
