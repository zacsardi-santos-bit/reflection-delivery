Redesign the goroutine pool library to simplify its public API and improve usability. Implement a single constructor for pool creation, streamline job submission, and enable dynamic resizing of the pool. Ensure that custom workers follow a unified interface for lifecycle management.

*   Implement a single constructor for pool creation:
    *   `NewFunc(n int, f func(interface{}) interface{}) *Pool` should return a ready-to-use pool with `n` workers.
    *   `NewCallback(n int) *Pool` should return a pool where each job payload is expected to be a `func()`.
    *   `New(n int, ctor func() Worker) *Pool` should create a pool using a constructor for custom workers.

*   Streamline job submission:
    *   `Pool.Process(payload interface{}) interface{}` should synchronously submit a job and return the result, panicking if the pool is closed.
    *   `Pool.ProcessTimed(payload interface{}, timeout time.Duration) (interface{}, error)` should return `ErrJobTimedOut` if the job times out and `ErrPoolNotRunning` if the pool is closed.

*   Enable dynamic resizing of the pool:
    *   `Pool.SetSize(n int)` should adjust the number of active workers, adding or removing them as necessary.
    *   `Pool.GetSize() int` should return the current number of active workers, returning 0 after `Close()` is called.
    *   `Pool.Close()` should terminate all workers, call `Terminate()` on each, and leave the pool in a state where `GetSize()` returns 0.

*   Implement a unified interface for custom workers:
    *   The `Worker` interface must include `Process(interface{}) interface{}`, `BlockUntilReady()`, `Interrupt()`, and `Terminate()` methods.
    *   Ensure `workerWrapper` struct has a `worker` field of type `Worker` for internal access.

*   Define and export error/value variables:
    *   `ErrPoolNotRunning`, `ErrJobNotFunc`, and `ErrJobTimedOut` must be exported in the tunny package.

*   Ensure pool's internal workers slice matches the specified size after creation with `NewFunc`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.