Implement a component to automatically share the host's container registry configuration with a Podman virtual machine on macOS or Windows. Ensure the configuration paths are correctly translated for use inside the VM and generate an automation script to create the necessary symbolic link.

*   Implement the `RegistryConfigurationImpl` class in `extensions/podman/packages/extension/src/configuration/registry-configuration.ts`:
    *   `getRegistryConfFilePath()`: Return a string path to 'registries.conf' in the user's home directory, compatible with both Windows and macOS.
    *   `getPathToRegistriesConfInsideVM()`: 
        *   On macOS, return the same path as `getRegistryConfFilePath()`.
        *   On Windows, convert the Windows-style path to a WSL-compatible path using the '/mnt/<drive>/' prefix.
    *   `getPlaybookScriptPath()`: Return a Promise resolving to a file path in `os.tmpdir()` named 'playbook-setup-registry-conf-file.yml'. Write a YAML file to this path with a symlink command: `sudo ln -s <vmPath> /etc/containers/registries.conf.d/999-podman-desktop-registries-from-host.conf`, where `<vmPath>` is from `getPathToRegistriesConfInsideVM()`.

*   Update `PodmanConfiguration` in `extensions/podman/packages/extension/src/podman-configuration.ts`:
    *   Add a `registryConfiguration` property implementing the `RegistryConfiguration` interface.

*   Modify the `createMachine` function in `extension.ts`:
    *   Call `podmanConfiguration.registryConfiguration.getPlaybookScriptPath()`.
    *   Append '--playbook' and the returned path to the 'machine init' command arguments when the installed Podman version supports it (e.g., version 5.4.0).

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.