## Description

The Gatsby Recipes system has two resource providers — one for managing Gatsby plugins and one for shadowing theme component files — that are not fully functional or properly tested.

The plugin resource tests used a single flat fixture directory, which means testing against different types of Gatsby configurations (such as a minimal project that starts with no plugins) wasn't possible. There's a real use case for applying recipes to freshly initialized projects that have a nearly empty configuration file.

The shadow file resource was also incomplete: it had placeholder test code with no real test data, relied on generating a temporary directory with a random name on every run, and the underlying implementation didn't properly handle creating intermediate directories or returning structured resource objects. The shadow file resource needs to support the full lifecycle — creating, reading, updating, destroying, and planning — for shadowing a component from a Gatsby theme.

## Expected Behavior

- The plugin resource should work correctly in a project with a minimal initial configuration (empty plugins list), not just in a project with many existing plugins.
- The shadow file resource should be fully implemented with proper create, read, update, destroy, and plan support.
- Shadow file operations should produce structured result objects describing the shadowed file, including the resolved destination path, the theme name, file contents, and a human-readable message.
- The file resource should ensure that parent directories exist before writing a file, so recipes don't fail when writing to nested paths that don't yet exist.

## Why This Matters

Recipe authors need these resources to work reliably across different project configurations, including minimal starter projects. Without these fixes, running a recipe to add a plugin or shadow a theme component can silently fail or crash depending on the state of the target project.
