## Description

The codebase has separate utilities for toggling agents and skills on and off across configuration scopes (user-level and workspace-level). These utilities implement nearly identical logic: check which scopes need to be changed, apply the change in each applicable scope, and return a structured result describing what was modified. This duplication means that every time a new type of feature needs enable/disable support, the same boilerplate has to be copied and adapted.

## Expected Behavior

- A shared, generic toggle utility should exist that handles the common logic of iterating settings scopes, building result objects, and handling error cases (such as attempting to disable a feature in an unsupported scope).
- The utility should accept a strategy object that encapsulates the feature-specific logic: how to check whether a scope needs enabling, how to apply the enable action, how to check whether a scope is already explicitly disabled, and how to apply the disable action.
- The utility should return structured results indicating which scopes were modified, which were already in the desired state, and whether the operation was a no-op, a success, or an error.
- The result objects should include the file path of the settings file for each affected scope.

## Why This Matters

Without this abstraction, adding enable/disable support for a new feature type requires duplicating the entire scope-iteration and result-building logic. With a shared utility, contributors can implement the feature-specific checking and modification behavior as a lightweight strategy, and the generic utility handles the rest. This reduces code duplication, makes the pattern consistent across feature types, and lowers the barrier to adding new toggleable features.
