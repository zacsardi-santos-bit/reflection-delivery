I'm working on converting the drain method system in our deployment tooling from synchronous to asynchronous. Right now, all the drain operations — checking if a task is being drained, starting a drain, stopping a drain, and checking if it's safe to kill a task — use blocking synchronous HTTP calls. We're moving to an async event loop-based architecture and these synchronous calls are incompatible with that.

I need all of these drain operations to become async coroutines that can be properly awaited. The underlying HTTP communication should also switch from the synchronous HTTP library to an async-compatible one, and the way we read response status codes and body text needs to match the async client's API. The deployment orchestration code that calls these drain methods also needs to be updated to properly await the results.

Could you help convert the drain method implementations and the parts of the deployment code that call them to be fully async?
