## Description

Pulumi programs in JavaScript and TypeScript currently must export their resources and outputs as a plain object at the module's top level. This works fine for synchronous programs, but developers who need to perform asynchronous operations at startup — such as awaiting configuration, calling external APIs, or performing dynamic setup before creating resources — have no clean or idiomatic way to do so. The workaround is to use immediately-invoked function expressions, which is awkward and not at all obvious.

## Expected Behavior

The Pulumi runtime should support exporting a single top-level function as the entire program entry point. When the exported value is a callable function, the runtime should:

- Automatically detect that the export is a function
- Invoke that function with no arguments
- Await the result (regardless of whether the function is synchronous, returns an asynchronous value, or is declared async)
- Deeply resolve any pending asynchronous values found in the returned value
- Use the fully resolved return value as the stack's outputs

Resources created inside the exported function must be tracked and registered just as if they had been created at the module's top level. If the function returns no value, the stack outputs should be empty. If the function returns a value alongside resource creation, both the resources and the outputs must be correctly registered.

## Why This Matters

This allows developers to write async-first Pulumi programs naturally — simply exporting an async function — without any special boilerplate or workarounds. It is a meaningful ergonomic improvement for programs that need async operations during initialization.
