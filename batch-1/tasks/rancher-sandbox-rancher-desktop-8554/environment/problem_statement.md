## Description

The virtual machine type and Rosetta translation toggle are currently stored under an "experimental" settings namespace. These features have been stable for a while and no longer belong behind an experimental prefix. Keeping them there is confusing for users and administrators who configure these settings, and it forces tooling to use overly verbose paths.

## Expected Behavior

- The virtual machine type and Rosetta preference settings should be accessible and configurable as top-level virtual machine properties, not as experimental sub-settings.
- Existing configurations that stored these values under the experimental namespace must be automatically migrated to the new location on the next application start.
- The settings version should be bumped to reflect this schema change.
- Serialization of the configuration to platform-native formats (macOS plist and Windows registry) must reflect the new structure, placing the virtual machine type and Rosetta fields in the main virtual machine section.
- The experimental settings section should no longer contain a virtual machine type or Rosetta field at all.

## Why This Matters

Users and administrators who manage Rancher Desktop through configuration files or deployment profiles currently have to use long, unintuitive "experimental" paths to set the virtual machine type and Rosetta preference. Now that these settings are stable, promoting them to the main virtual machine settings area makes them more discoverable and removes a false impression that they are still in preview. Old configurations from before this change should continue to work seamlessly via automatic migration.
