## Description

The inventory version 2 plugin system currently has an inconsistency: target-discovery plugins and configuration plugins use different syntaxes in inventory files. Target plugins must be declared under a separate dedicated key at the group level using a plain identifier, while config plugins are declared inline within their section using an underscore-prefixed marker. This split syntax is confusing and makes inventory files harder to read.

Additionally, the internal hook names that plugins register to advertise their capabilities are inconsistently named, which makes the plugin contract harder to understand and extend.

## Expected Behavior

- Target-discovery plugins should be declared inline within the standard targets list using the same underscore-prefixed syntax already used by config plugins, rather than through a separate group-level key.
- Using the old separate key for target plugins should produce a clear error message explaining that the syntax is no longer valid and how to migrate to the new format.
- The hook names plugins register to declare their capabilities should be updated to be more consistent and descriptive.
- When a plugin is referenced for a capability it does not support, the system should produce a clear error message identifying which plugin and which capability is missing.

## Why This Matters

This makes the inventory format more consistent and easier to understand — users no longer need to learn two different syntaxes depending on whether they are providing targets or configuration values via a plugin. Clearer error messages when plugins don't support a requested capability also make debugging inventory files significantly easier.
