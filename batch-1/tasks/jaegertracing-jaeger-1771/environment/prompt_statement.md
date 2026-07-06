I'm working on the badger storage backend for Jaeger and I'd like to clean up how timestamps are handled during span writes. Right now, every time a span is written, the current time and the expiration time are recomputed separately for each index entry created — service index, operation index, duration index, tag indexes, and so on. This means many redundant conversions happen on the hot write path.

There's also a type mismatch I'd like to fix: the in-memory service and operation caches store expiration timestamps as signed integers, but the storage engine works with unsigned integers natively. Having to cast between them is error-prone and unnecessary.

I'd like the start time and expiration time to be computed once per span write and then threaded through to all the helper functions that build index keys, trace keys, and update the cache. The cache itself should be updated to use unsigned integers for its timestamp maps so the types are consistent throughout. The functions that construct storage keys should accept the pre-computed unsigned timestamp directly rather than receiving a time value and converting it themselves.

After these changes, I'd also expect trace query results to come back in a consistent newest-to-oldest order.
