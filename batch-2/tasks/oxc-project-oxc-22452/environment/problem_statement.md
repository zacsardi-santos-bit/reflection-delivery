## Description

When users run a formatter or linter tool with a TypeScript config file on an older version of Node.js that does not natively support loading TypeScript modules, they get a low-level, cryptic error that gives no indication of why it failed or what to do about it. The raw error is confusing and unhelpful because it doesn't explain the minimum Node.js version required or suggest a workaround.

## Expected Behavior

- There should be a way to detect whether a given file path or URL refers to a TypeScript module (i.e., has a TypeScript file extension).
- When a module-loading failure occurs specifically because the running Node.js version does not support TypeScript files, a clear, actionable hint should be produced that:
  - Includes the original error message for context
  - States the Node.js version range needed for TypeScript config support
  - Reports the currently running Node.js version
  - Suggests either upgrading Node.js or switching to a JSON config file instead
- The hint should only appear for this specific failure mode; unrelated errors or non-TypeScript config files should not trigger it.
- The supported Node.js version range should be exported as a named constant so it can be reused consistently across the codebase.

## Why This Matters

Without this, users who encounter the TypeScript-loading error have no way of knowing whether they need to upgrade Node.js or change their config file format. A targeted, informative message dramatically reduces confusion and support burden.
