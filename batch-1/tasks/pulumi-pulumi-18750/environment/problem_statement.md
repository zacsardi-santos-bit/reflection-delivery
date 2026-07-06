## Description

The Node.js SDK automation test suite is completely broken — no tests can run because the TypeScript compilation fails before any test code executes. The error occurs because a dependency used in the runtime code lacks type declarations that the current TypeScript runner can find, causing a type inference failure that aborts compilation.

## Expected Behavior

- The automation tests should compile and run successfully without TypeScript errors related to missing type declarations for third-party modules.
- Tests that set configuration on multiple stacks concurrently should succeed when stack names include full organization and project context (the three-part "org/project/stack" format), not just bare stack names.

## Current Behavior

Running the test suite causes a TypeScript compilation failure. The error is about a module used internally that has no accessible type declarations, so the compiler cannot infer its types. This kills the entire test run — not just one test.

## Why This Matters

With the test suite unable to compile, there is no safety net for any changes to the Node.js SDK automation API. Developers can't validate their changes or iterate confidently. The fix involves switching the TypeScript transpilation approach for the test runner to one that is more tolerant of untyped third-party modules, restoring the ability to run the full automation test suite.
