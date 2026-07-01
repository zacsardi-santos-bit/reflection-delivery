## Description

Kubebuilder projects need a way for plugins to store and retrieve their own configuration data as part of the project's config file. Currently, the project config only tracks core fields (repository, domain, version), and there is no standard way for a plugin to persist custom configuration alongside these fields.

## Expected Behavior

- The project configuration file should support an optional section for plugin-specific data when the project is using a sufficiently recent config version.
- Plugins should be able to encode their own configuration objects into the project config under a unique key, and later decode that data back into a typed object.
- Attempting to use plugin config storage on an older project config version should fail with an error, since that version does not support this feature.
- When a project config is saved to disk, plugin data for newer-version projects should be serialized into the config file under a dedicated section.
- For older config versions, no plugin data should be written, even if extra fields are present in memory.
- Loading the config file back from disk should correctly reconstruct the plugin data, including nested maps and list values.
- Saving a config without a valid file path should fail with an error.

## Why This Matters

Plugins are a core extension mechanism in kubebuilder, and they need a standard, version-aware way to persist their settings alongside the project config. Without this, each plugin would need to manage its own separate config file or use ad-hoc approaches, leading to inconsistency. This feature enables plugins to store structured configuration objects in the project file in a safe, versioned manner.
