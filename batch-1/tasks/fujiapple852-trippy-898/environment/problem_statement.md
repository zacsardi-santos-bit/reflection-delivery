## Description

The public API of the tracing library uses verbose, redundant type names. The main configuration type and related types all carry a prefix that simply repeats the module name, which goes against common conventions for library design where types are typically referred to by short names within their own module. For example, when using the tracer configuration from the tracing module, consumers have to spell out the full prefixed name even though the module context already makes the prefix redundant.

Beyond the naming issue, the current API only supports constructing configuration objects via large multi-argument constructors, which are hard to use and maintain. A builder pattern would make it much easier to construct configuration objects by setting only the fields that differ from sensible defaults.

## Expected Behavior

- The primary tracer algorithm configuration type should be accessible under a shorter, module-scoped name without redundant prefix
- A builder type should be available for constructing the tracer configuration step-by-step, with chainable setter methods and a final build method
- A builder type should similarly be available for constructing the network channel configuration
- Both configuration types should implement default values and support equality comparison
- All existing functionality should continue to work after the rename

## Why This Matters

These improvements make the library easier to use: shorter type names reduce boilerplate for callers, and builder patterns allow incremental construction of complex configuration objects using only the defaults that need overriding. This is especially helpful for users who only need to customize a few settings.
