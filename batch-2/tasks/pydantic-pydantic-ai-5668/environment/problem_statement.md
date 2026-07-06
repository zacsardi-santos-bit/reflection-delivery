## Description

When a tool preparation callback accidentally returns nothing instead of an explicit empty list, the framework currently raises a confusing low-level error that doesn't identify which callback caused the problem. The error message gives no useful context to the developer — it doesn't say which prepare function was at fault or what they should do to fix it.

## Expected Behavior

- When a tool preparation callback returns nothing, a clear user-facing error should be raised that includes the name of the offending callback function.
- The error should make it obvious that returning nothing is not allowed — developers should return an empty list if they want to disable all tools.
- Type checkers should be able to catch this mistake at development time: the type signature for prepare callbacks should not allow returning nothing, so that tools producing static type annotations flag it immediately.

## Why This Matters

Developers using prepare callbacks to filter or modify tools at runtime can easily make this mistake. A clear, named error dramatically reduces debugging time. Tightening the type annotation means the issue can be caught before the code even runs, improving the development experience.
