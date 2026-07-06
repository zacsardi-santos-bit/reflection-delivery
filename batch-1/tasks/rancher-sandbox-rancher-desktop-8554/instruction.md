Update the Rancher Desktop settings schema to promote the virtual machine type and Rosetta preference from the "experimental" namespace to the main virtual machine settings. Implement automatic migration for existing configurations and update the settings serialization to reflect these changes.

*   Change the settings version:
    *   Set `CURRENT_SETTINGS_VERSION` to 15 in `pkg/rancher-desktop/config/settings.ts`.

*   Update the virtualMachine settings schema:
    *   Include 'type' and 'useRosetta' as direct properties in the virtualMachine section.
    *   Ensure 'type' is a string with valid values 'qemu' and 'vz' for macOS only.
    *   Ensure 'useRosetta' is a boolean for macOS only.
    *   Remove these fields from `experimental.virtualMachine`.

*   Modify serialization outputs:
    *   Ensure the plist output includes `<key>type</key><string>qemu</string>` and `<key>useRosetta</key><false/>` in the virtualMachine dict.
    *   Ensure the Windows registry output includes 'type' and 'useRosetta' in the virtualMachine section.
    *   Ensure the experimental.virtualMachine section does not contain 'type' or 'useRosetta'.

*   Implement migration logic:
    *   Add a function at key 14 in `updateTable` in `pkg/rancher-desktop/config/settingsImpl.ts` to:
        *   Move `experimental.virtualMachine.type` to `virtualMachine.type`.
        *   Move `experimental.virtualMachine.useRosetta` to `virtualMachine.useRosetta`.
        *   Remove `experimental.virtualMachine` if it becomes empty.

*   Update exports and default settings:
    *   Export `VMType` from `pkg/rancher-desktop/config/settings.ts`.
    *   Set `defaultSettings.virtualMachine.type` to `VMType.QEMU`.
    *   Set `defaultSettings.virtualMachine.useRosetta` to `false`.
    *   Ensure these fields are not in `defaultSettings.experimental.virtualMachine`.

*   Update OpenAPI specification:
    *   Declare 'type' as a string property with enum [qemu, vz] and `x-rd-platforms: [darwin]` under the virtualMachine schema in `pkg/rancher-desktop/assets/specs/command-api.yaml`.
    *   Declare 'useRosetta' as a boolean property with `x-rd-platforms: [darwin]` under the virtualMachine schema.
    *   Remove these properties from `experimental.virtualMachine`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.