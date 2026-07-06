## Description

The mutex utility package in GoFrame is currently empty — it has no implementation files, only the package declaration. This means any code that tries to use mutex types or the package-level constructor from this package will fail to compile.

We need a complete implementation of the mutex utilities in this package. Specifically, the package should offer:

- A basic exclusive-lock type that supports acquiring a lock, executing a callback, and automatically releasing the lock when done.
- A non-blocking variant of the above that only runs the callback if the lock is immediately available.
- A read-write lock type with the same callback-based API for both write locks and read locks, plus non-blocking ("try") variants for each.
- A constructor function that creates and returns a new instance of the read-write lock type.

## Expected Behavior

- When a write lock is held by one goroutine, other goroutines attempting to acquire any lock (write or read) should block until the lock is released.
- Multiple goroutines should be able to acquire a read lock at the same time (when no write lock is held).
- The non-blocking lock methods should immediately return without executing the callback if the lock cannot be obtained.
- After a write lock is released, all pending goroutines trying to acquire a read lock non-blockingly should succeed.

## Why This Matters

Without these implementations, developers using GoFrame cannot take advantage of the mutex utilities the package is intended to provide. This is a foundational concurrency primitive used throughout the framework.
