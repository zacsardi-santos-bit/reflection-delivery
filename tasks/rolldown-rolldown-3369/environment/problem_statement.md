## Description

When a CommonJS module conditionally re-exports another module at runtime — selecting from multiple required modules using a conditional expression — and an ESM file imports it as a default import, the bundler incorrectly skips the interop compatibility layer. This causes the generated code to call or access properties directly on the interop wrapper object, bypassing the intermediate layer where the actual exported value is stored.

## Example Scenario

Consider a module that exports one of two other CommonJS modules depending on a runtime condition. An ESM consumer imports this as a default import and calls it as a function. The bundler should generate code that goes through the interop compatibility accessor to reach the actual function — but instead it tries to call the wrapper object directly, which fails at runtime.

## Expected Behavior

- When a CommonJS module uses a conditional require expression as its exported value, the bundled output should access the imported value through the interop compatibility property, not directly on the wrapper.
- Similarly, when accessing any named property on a namespace binding that wraps a CommonJS module, the bundled output should route through the interop compatibility layer — not access the property directly on the wrapper object.

## Why This Matters

Users who combine CommonJS modules that conditionally select dependencies at runtime with modern ESM imports will get silently broken bundles. Function calls that should succeed will fail, or property accesses will return the wrong value, because the generated code is missing the required property access step in the interop chain.
