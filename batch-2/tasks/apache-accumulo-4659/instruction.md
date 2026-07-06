Implement a mechanism to allow Accumulo server processes to accept a factory function for creating their data context, enabling the injection of alternative implementations for testing. Create a test-only server context that simulates ambiguous conditional mutation outcomes and update the conditional write interceptor interface for more flexible control.

*   Update the `AbstractServer` class:
    *   Modify the constructor to accept a factory function (`Function<SiteConfiguration, ServerContext>`) for creating the server context.
*   Modify server classes to use the context factory:
    *   Update `Manager` with a protected constructor: `Manager(ConfigOpts opts, Function<SiteConfiguration, ServerContext> serverContextFactory, String[] args)`.
    *   Update `TabletServer` with a protected constructor: `TabletServer(ConfigOpts opts, Function<SiteConfiguration, ServerContext> serverContextFactory, String[] args)`.
    *   Update other subclasses (`Compactor`, `SimpleGarbageCollector`, `Monitor`, `ScanServer`) to pass `ServerContext::new` as the factory argument.
*   Revise the `ConditionalWriterInterceptor` interface:
    *   Replace old methods with:
        *   `default Iterator<ConditionalWriter.Result> write(ConditionalWriter writer, Iterator<ConditionalMutation> mutations)`
        *   `default Result write(ConditionalWriter writer, ConditionalMutation mutation)`
    *   Ensure default implementations delegate to `writer.write(...)`.
*   Update `ConditionalWriterDelegator` to use the new `write()` methods.
*   Modify the `withStatus` factory method on `ConditionalWriterInterceptor` to override the new `write()` methods.
*   Implement `FlakyInterceptor`:
    *   Randomly choose one of three outcomes for each mutation: 
        *   Do not write and return UNKNOWN.
        *   Write and return UNKNOWN.
        *   Write and return actual status.
*   Create `FlakyAmpleServerContext`:
    *   Extend `ServerContext` and override `getAmple()` to return a `TestAmple` using `FlakyInterceptor`.
*   Extend `Manager` and `TabletServer` for testing:
    *   Implement `FlakyAmpleManager` with constructor: `FlakyAmpleManager(ConfigOpts opts, String[] args)`.
    *   Implement `FlakyAmpleTserver` with constructor: `FlakyAmpleTserver(ConfigOpts opts, String[] args)`.
*   Update `FlakyFateManager` to match the new `Manager` constructor signature.
*   Implement `ComprehensiveFlakyAmpleIT`:
    *   Extend `ComprehensiveBaseIT`, start a mini cluster with `FlakyAmpleManager` and `FlakyAmpleTserver`, and run the full test suite.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.