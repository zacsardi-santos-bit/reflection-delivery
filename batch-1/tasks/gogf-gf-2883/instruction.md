Implement the mutex utility package in GoFrame by creating two mutex types: `Mutex` and `RWMutex`. These types should support exclusive and read-write locking, respectively, with both blocking and non-blocking methods. Ensure that the package includes a constructor function for creating new instances of `RWMutex`.

*   Implement the `Mutex` type in `os/gmutex/gmutex_mutex.go` (or `os/gmutex/*.go`).
    *   Ensure `Mutex` is usable as a zero value and wraps or embeds `sync.Mutex`.
    *   Provide a `LockFunc(f func())` method that acquires the write lock, executes the function `f`, and releases the lock after `f` returns.
    *   Provide a `TryLockFunc(f func()) bool` method that attempts to acquire the write lock non-blockingly. If successful, execute `f` and return `true`; otherwise, return `false`.

*   Implement the `RWMutex` type in `os/gmutex/gmutex_rwmutex.go` (or `os/gmutex/*.go`).
    *   Ensure `RWMutex` is usable as a zero value and wraps or embeds `sync.RWMutex`.
    *   Provide a `LockFunc(f func())` method that acquires the write lock, executes `f`, and releases the lock after `f` returns.
    *   Provide a `TryLockFunc(f func()) bool` method that attempts to acquire the write lock non-blockingly. If successful, execute `f` and return `true`; otherwise, return `false`.
    *   Provide a `RLockFunc(f func())` method that acquires the read lock, executes `f`, and releases the lock after `f` returns. Allow multiple concurrent read locks unless a write lock is held.
    *   Provide a `TryRLockFunc(f func()) bool` method that attempts to acquire the read lock non-blockingly. If a write lock is held, return `false`; otherwise, execute `f` and return `true`.

*   Implement the `New` function in `os/gmutex/gmutex.go` (or `os/gmutex/*.go`).
    *   Define `New() *RWMutex` to create and return a new instance of `RWMutex`.
    *   Ensure the returned `RWMutex` supports `Lock`, `Unlock`, `TryLock`, `RLock`, `RUnlock`, and `TryRLock` operations.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.