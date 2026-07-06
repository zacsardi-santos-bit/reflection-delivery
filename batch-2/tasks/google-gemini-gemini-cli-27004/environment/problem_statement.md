## Description

The sandbox environment on Linux has several reliability and security issues related to how the host user is mapped into the container. These problems prevent the sandbox from working correctly on several popular Linux distributions and in edge-case environments.

## Problems

1. **Distribution detection is too narrow**: The logic that decides whether to map the host user into the sandbox only recognizes a small set of Linux distributions. Users on NixOS, Arch Linux, and distributions derived from recognized distros (like Ubuntu-based variants) see incorrect behavior. Additionally, the parser for the OS identification file does not handle quoted distribution ID values, which is valid syntax used by many distributions.

2. **No warning on unrecognized distributions**: When the host system is running a distribution that isn't recognized and the UID doesn't match expectations, the tool silently proceeds in a potentially broken configuration rather than warning the user. When the host user is root (UID 0), no warning should be emitted.

3. **Fragile user-creation script**: The shell script the sandbox generates to create a matching user inside the container assumes that user-creation tools are always available. On minimal container images (e.g., distroless or NixOS-based), these tools may not exist. The script also hardcodes a username instead of dynamically looking it up by UID, which breaks when the UID is already mapped to a different username. Proper error messages and exit codes are missing when these failure cases occur.

4. **Home directory paths with special characters are not escaped**: If the host home directory contains spaces or shell-special characters (like backticks), the generated entrypoint command fails or creates a shell injection risk because the path is embedded unquoted.

## Expected Behavior

- NixOS, Arch Linux, and distributions related to recognized distros must be detected as supported
- Quoted values in the OS identification file must be parsed correctly
- When a distribution is unrecognized and the host UID is non-zero, a clear warning must be logged
- The user-creation entrypoint script must check for the availability of user-creation tools, look up the username dynamically by UID, and handle all failure cases with informative error messages and a non-zero exit
- Home directory paths must be properly quoted when embedded in shell commands

## Why This Matters

Without these fixes, users on NixOS or Arch Linux cannot use the sandbox correctly, and users whose home directory contains spaces or special characters may encounter sandbox failures or security issues.
