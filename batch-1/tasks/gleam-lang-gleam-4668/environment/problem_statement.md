## Description

The Gleam compiler currently allows a module to be imported multiple times within the same source file, as long as each import uses a different local alias. However, this is almost always a mistake — developers rarely intend to import the same module under two different names — and the compiler gives no feedback when it happens.

## Expected Behavior

- When the same module is imported more than once in a file (regardless of whether each import uses a different alias), the compiler should emit a warning to alert the developer.
- The warning should clearly identify which module was imported redundantly.
- The warning should point to both import locations in the source, so the developer can easily find and clean them up.

## Why This Matters

Accidentally importing the same module twice under different aliases can cause subtle confusion — code might appear to use two distinct modules but in fact uses the same one. Catching this at compile time with a descriptive warning makes the problem immediately obvious and actionable, and keeps codebases clean.
