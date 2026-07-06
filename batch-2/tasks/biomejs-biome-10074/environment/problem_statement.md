## Description

The import organizer in Biome currently has no way to create import groups based on whether an import is a "bare" (side-effect only) import versus one that binds a name. Many JavaScript projects use side-effect imports for polyfills, CSS files, and other initialization modules that need to be loaded in a specific order or kept visually separate from regular imports. Without the ability to group imports by their "bare" vs "binding" nature, the import organizer cannot correctly handle these cases.

## Expected Behavior

- Developers should be able to configure an import group that specifically targets side-effect-only imports (bare imports — those without any imported bindings).
- Developers should be able to configure a group that targets all imports that do have named or default bindings (non-bare imports).
- The bare/non-bare grouping should work together with other existing group types and blank line separators.
- It should be possible to further narrow a bare import group by also filtering on the import's source path using a glob pattern (e.g., only CSS imports).
- A new option should allow bare imports to be sorted alphabetically within their group.

## Why This Matters

Without this feature, teams using polyfills, CSS-in-JS, or other side-effect-first patterns cannot rely on the import organizer to maintain the correct file structure. This improvement makes it possible to enforce consistent import ordering for projects that care about both the structure and execution order of their imports.
