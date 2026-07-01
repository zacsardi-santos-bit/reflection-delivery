## Description

The BAML TypeScript code generator produces files with linting suppression directives in the wrong order. Specifically, the ESLint suppression comment is placed at the end of the suppression block instead of at the beginning, which causes unexpected linting errors when ESLint runs on the generated files.

## Expected Behavior

- The ESLint suppression directive should appear as the **first** comment in the linting suppression header block of every generated TypeScript file
- The ordering should be: ESLint suppression first, then tslint suppression, then the TypeScript type-check skip directive, then the formatter suppression

## Current (Broken) Behavior

The generated TypeScript files currently place the ESLint suppression comment **last** in the header block, after the TypeScript skip-checking directive. Because linting tools process files from top to bottom, the ESLint suppression isn't applied before the TypeScript directive, leading to spurious linting errors in auto-generated code.

## Why This Matters

Developers using BAML's generated TypeScript clients get ESLint errors in auto-generated files that should be fully suppressed. This is confusing because the suppression comment is present — it's just in the wrong position. Fixing the order ensures all generated files are truly lint-clean out of the box.
