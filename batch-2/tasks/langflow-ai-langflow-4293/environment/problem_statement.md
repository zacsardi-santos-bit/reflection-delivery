# Detect and prevent blocking I/O in async test contexts

## Description

Our test suite runs many tests in an async context, but several operations in the codebase perform blocking I/O directly on the event loop thread without any detection or warning. These blocking calls can silently degrade test performance, cause subtle timing issues, and make it hard to reason about the async behavior of the code under test.

We need a way to automatically detect when blocking I/O operations (file reads/writes, network socket calls, sleeping) are executed while an async event loop is running, and raise an error when this happens. This will force developers to move blocking calls to thread pools.

## Expected Behavior

- A blocking-detection mechanism should be available that, once initialized, raises a distinct error whenever a blocking call (such as sleeping, file I/O, or socket communication) is made from within a running async event loop.
- Blocking calls made outside of any async context (plain synchronous code) should continue to work normally.
- A new async-safe version of the component list loading function should be provided so that code in async tests can call it without triggering the blocking error.
- Certain special cases (e.g., calls from within a debugger or from test infrastructure that rewrites bytecode) should be allowed through without raising the error.

## Why This Matters

Without this detection, blocking calls on the event loop thread fail silently, making it difficult to catch regressions where synchronous I/O sneaks into async code paths. With the detection in place, tests immediately fail when a blocking call is made on the event loop, making it clear which code needs to be fixed.
