## Description

The drain method system used during service deployments currently makes blocking synchronous HTTP calls to manage task lifecycle (draining, stopping drain, checking drain status, and checking kill safety). This approach is incompatible with asynchronous I/O patterns and event loops that the broader system is moving toward.

All drain-related operations need to be converted to asynchronous, non-blocking coroutines so they integrate properly with the async event loop used by the deployment orchestration layer.

## Expected Behavior

- All core drain operations — starting a drain, stopping a drain, checking if a task is draining, and checking if a task is safe to kill — should return awaitables that callers can await.
- The HTTP communication layer within the drain methods should use an async-compatible HTTP client instead of the synchronous blocking HTTP library.
- Response attributes should align with the async HTTP client conventions (e.g., status codes and response bodies accessed appropriately for async responses).
- The deployment orchestration code that invokes these drain methods should properly await their results.

## Why This Matters

As the system adopts async I/O patterns, synchronous blocking calls in the drain path would block the event loop, causing performance issues and incompatibility with the async orchestration layer. Converting these methods to async coroutines ensures the drain lifecycle operations integrate cleanly with async deployment workflows without blocking.
