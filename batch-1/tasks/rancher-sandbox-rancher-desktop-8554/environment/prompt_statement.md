I'm working on Rancher Desktop and want to graduate the virtual machine type and Rosetta emulation settings out of the "experimental" namespace. Right now, to configure which hypervisor to use or whether to enable Rosetta translation, you have to reference a path that includes "experimental" — but these features have been stable for a long time and should live directly under the main virtual machine settings.

I need the settings schema to be updated so that the virtual machine type and Rosetta preference are top-level virtual machine properties. The experimental settings section should no longer include those fields. The settings version should be bumped to reflect this schema change.

I also need a migration step so that existing user configurations that stored these values under the old experimental path are automatically moved to the new path when the application starts. After migration, the old experimental virtual machine sub-object should be cleaned up if it has no remaining properties.

The API specification file that drives CLI and profile configuration generation needs to be updated to reflect the new field locations. Serialization of settings to platform formats like macOS property lists and Windows registry files should include the virtual machine type and Rosetta fields in the main virtual machine section going forward.
