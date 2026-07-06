I'm building a custom runtime on top of the boa JavaScript engine and I've run into a fundamental limitation with the event loop.

*   Must add an `inspect_context_async` constructor to `TestAction` (in the `#[cfg(test)]` section of `core/engine/src/lib.rs`) that accepts an async closure implementing `AsyncFnOnce(&mut Context) + 'static` and returns a `TestAction`. The corresponding `Inner::InspectContextAsync` variant must store a `Box<dyn for<'a> FnOnce(&'a mut Context) -> PinBoxFuture<'a>>` where `PinBoxFuture<'a> = Pin<Box<dyn Future<Output = ()> + 'a>>`. The test runner (`run_test_actions_with`) must execute this variant by calling `futures_lite::future::block_on(op(context))`.

*   The `run_jobs_async` method on `SimpleJobExecutor` must yield to the outer async executor between processing iterations (e.g., via `future::yield_now().await`). As a result, calling `future::poll_once` on the future returned by `run_jobs_async` must return `None` (pending) when there are async futures still running in the group, even if all named job queues (promise, async, timeout, generic) are empty.

*   The termination check in `run_jobs_async` must occur AFTER processing the current iteration's jobs (timeout jobs, generic jobs, promise jobs, async group poll). This ensures that a `GenericJob` enqueued on the context between two consecutive polls of the event loop future is executed during the second poll, not missed.

*   When `future::poll_once` is applied to the `run_jobs_async` future and all job queues are empty AND no async futures remain in the running group, the future must resolve and `poll_once` must return `Some(Ok(()))`. This is the correct termination signal for one-shot jobs like `setTimeout`.

*   When an async job that continuously yields (e.g., an infinite loop calling `future::yield_now().await`) is enqueued, each call to `poll_once` on the event loop future must return `None` (the event loop keeps running), and any `GenericJob` enqueued after the first poll must be executed during a subsequent poll.

*   The `TestAction` type (and `Inner` enum) must NOT derive `Clone` once `InspectContextAsync` is introduced, since the boxed closure is not `Clone`.

*   The existing `Context::downcast_job_executor::<SimpleJobExecutor>()` API must return `Option<Rc<SimpleJobExecutor>>`, allowing callers to retrieve the concrete executor and call `run_jobs_async` directly on it.

*   The `set_interval_zero_delay_terminates_with_advancing_clock` test in `core/runtime/src/interval/tests.rs` must be removed. The interval tests (`set_timeout_cancel`, `set_timeout_delay`, `set_interval_delay`) must be updated to use the async event loop pattern: obtain the executor via `downcast_job_executor`, pin a `poll_once` future over `run_jobs_async`, and drive it with `.await` instead of calling `run_jobs()`.

*   The test module declaration in `core/engine/src/tests/mod.rs` must replace `mod generators` with `mod job`, and the file `core/engine/src/tests/generators.rs` must be removed.


*   Interface details: Type: Method
Name: inspect_context_async
Location: core/engine/src/lib.rs (test module, `#[cfg(test)]`)
Signature: pub(crate) fn inspect_context_async(op: impl AsyncFnOnce(&mut Context) + 'static) -> Self
Description: Creates a new TestAction variant that executes an async closure with the active JavaScript context. The closure receives a `&mut Context`. When the test runner encounters this action, it executes the closure using `futures_lite::future::block_on`. This is the only way to run async Rust code that interacts with the live context during a test.

Type: Enum Variant
Name: InspectContextAsync
Location: core/engine/src/lib.rs (test module, inside the `Inner` enum, `#[cfg(test)]`)
Signature: InspectContextAsync { op: Box<dyn for<'a> FnOnce(&'a mut Context) -> Pin<Box<dyn Future<Output = ()> + 'a>>> }
Description: Internal variant of the `Inner` enum (used by `TestAction`) that stores a boxed async operation taking a `&mut Context`. The type alias `PinBoxFuture<'a> = Pin<Box<dyn Future<Output = ()> + 'a>>` is used for the return type. Must be handled in `run_test_actions_with` by calling `futures_lite::future::block_on(op(context))`.

Type: Async Method (existing, behavior change required)
Name: run_jobs_async
Location: core/engine/src/job.rs — implemented on `SimpleJobExecutor` via `JobExecutor` trait
Signature: async fn run_jobs_async(self: Rc<Self>, context: &RefCell<&mut Context>) -> JsResult<()>
Description: Runs the JavaScript job event loop asynchronously. Must yield between processing iterations (via `future::yield_now().await` or equivalent) so that callers using `future::poll_once` receive `None` (pending) when work is still in progress. When all job queues are empty AND no async futures are currently running, the future must resolve (return `Ok(())`). Async jobs in the queue are moved into a running group each iteration; the group is polled once per iteration. The termination check must occur AFTER processing jobs in the current iteration (not before), ensuring that jobs enqueued externally between polls are processed before the loop decides to exit.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.