I'm working on the CloudFoundry CLI and I need to add support for attaching distributed tracing headers to outgoing HTTP requests. Right now, when the CLI talks to the Cloud Controller, the Router API, or UAA, none of the requests carry any tracing context, which makes it hard to correlate CLI operations with backend service logs.

I'd like to add connection middleware wrappers — one for each API client — that inject B3 trace headers (a trace ID and a span ID) into every outgoing request. The trace ID should be supplied at construction time, and the span ID should be randomly generated. Importantly, if a request already has those headers set, the middleware should leave them alone rather than overwriting them.

I also need utility functions to generate random trace identifiers: one that always produces a 32-character identifier, and another that accepts a desired length and returns a random string of exactly that length.
