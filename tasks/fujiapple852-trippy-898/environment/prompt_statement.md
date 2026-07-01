I'm working on cleaning up the public API of the tracing library. The main configuration type for the tracer is currently exposed under a long, prefixed name that repeats the module context, which feels redundant and verbose. I'd like to rename it to just a short, unadorned name within the tracing module. The same applies to a few other related types that carry unnecessary prefixes.

At the same time, I'd like to add builder types for both the tracer configuration and the network channel configuration. Right now, constructing these objects requires passing a lot of arguments in order, which is hard to work with. Builder types with chainable setter methods and sensible defaults would be much more ergonomic.

Both configuration types should support equality comparison and have reasonable default values. The builders should start from the defaults and allow callers to override just the settings they care about before calling a final build method to produce the configured object.
