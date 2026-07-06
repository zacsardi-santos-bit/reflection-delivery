I'm working with the GoFrame framework and noticed that the mutex utility package is completely empty — there are no implementation files, just a package declaration. As a result, any attempt to use the mutex types from this package fails to compile.

I need the package to provide two concrete mutex types: one that supports only exclusive (write) locking, and one that supports both exclusive and shared (read) locking. Both types should support a functional API where you pass a callback that automatically runs while the lock is held and is released when the callback returns — so you never have to remember to manually unlock. They should also have non-blocking ("try") variants that only execute the callback if the lock can be acquired immediately, skipping it otherwise.

The read-write variant should allow multiple goroutines to hold a read lock at the same time, but block all readers if a write lock is held. The non-blocking read-lock version should return immediately without running the callback if a write lock is currently active.

Additionally, there should be a constructor function that creates and returns a new instance of the read-write lock type. Both types should be usable as zero values (no initialization required beyond declaration).
