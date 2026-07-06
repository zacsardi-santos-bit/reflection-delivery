I'm working on a logging library for a Go project and have run into a panic that happens in production. When we create a span from a context that has a timeout, and that timeout expires before we call the span's done function, the logging system panics. Since timeouts are completely normal in our workloads, this crash keeps happening and needs to be fixed. The span should complete gracefully whether or not the underlying context is still alive when done is called.

There's also a related issue: the system panics when it encounters an unrecognized log level value internally, which should instead be handled gracefully — there's no good reason for that to be a fatal error.

Additionally, I need a test helper that creates an info-level logger (similar to an existing debug-level bench logger helper) so I can benchmark the case where debug messages are filtered out. When debug messages are sent to an info-level logger, nothing should be written to the output.
