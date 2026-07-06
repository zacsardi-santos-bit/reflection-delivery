## Description

The Gleam compiler does not properly detect and report duplicate module definitions that arise from dependency conflicts. When two different packages in a project both define a module with the same name, the compiler should catch this and produce a clear, helpful error message — but currently this situation is either missed or produces a confusing error that doesn't identify which packages are in conflict.

Additionally, when a duplicate module arises within the same package (two files in the same package resolving to the same module name), the error message should show the relevant file paths to help pinpoint the problem, rather than package names (which would be identical and therefore unhelpful).

## Expected Behavior

- When a module name is defined by two different packages, the compiler should produce an error that clearly states which two packages are both claiming that module name.
- When a module name is defined twice within the same package, the compiler should produce an error that shows the two file paths involved.
- The error messages should use distinct, readable formats for each case so developers can immediately understand the nature of the conflict.

## Why This Matters

Dependency conflicts involving duplicate module names can be extremely confusing to diagnose. Without a clear error, developers may see cryptic compilation failures or incorrect behavior without understanding the root cause. Providing distinct, informative error messages for both cross-package and within-package conflicts makes it much easier to identify and fix the problem.
