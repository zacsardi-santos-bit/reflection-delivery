I'm updating our Fireworks AI chat model integration to work with the new 1.x Fireworks SDK, and the old 0.x code just breaks against it because the error hierarchy, import paths, and async method naming all changed. Right now users hit import errors and test failures the moment they upgrade, so I need to fix the integration layer to match the new conventions while keeping existing user code working.

First the error classes. They all need to come from the top-level Fireworks package now instead of some submodule, and two got renamed: the invalid-request error is now a bad-request error, and the service-unavailable error is now an internal-server error. The new ones also can't just take a message string anymore, they need an HTTP response object plus a body at construction time, so wherever we build these I have to pass those through.

On the async side, the async client dropped its separate method name and now just uses the same call method name as the sync client, and it's a real async coroutine (not a regular function handing back an awaitable), so make sure it's defined with async def.

Streaming usage tracking changed too. The stream options have to go nested inside an extra body field now rather than being passed as top-level params. When streaming usage is off, don't add any extra body at all. And if someone provides stream options both top-level and inside the extra body, the extra-body one wins and we log a warning about the duplicate.

Couple more things: disable the SDK's own built-in retry during client construction so we're not double-retrying (once in the SDK, once in our layer), and auto-convert legacy tuple-style timeout values into whatever object format the new SDK expects so old configs keep working.

Last one, when a prompt-too-long / context-overflow error shows up and we promote it to our custom context-overflow exception type, carry over the underlying error's HTTP response metadata (status code) and body so downstream code that inspects those still works.
