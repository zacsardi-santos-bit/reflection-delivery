I'm working on removing a deprecated, unmaintained third-party HTTP client library from our Node.js APM agent project. This library is used in our test suite to make HTTP requests to test servers, but it's no longer maintained and we want to eliminate it as a dependency entirely.

The affected tests span several areas: cloud metadata tests, HTTP instrumentation tests, and sanitize-field-names tests across multiple web framework integrations. All of these currently use the deprecated library to make HTTP requests during testing.

I need the library removed from our declared project dependencies so it's never installed, and all usages in the test files replaced with built-in HTTP support. Since several tests need to make form-encoded POST requests (something the old library handled automatically), a shared helper utility for that purpose should be created and reused across the relevant test files rather than duplicating the logic.
