Update the sandbox environment on Linux to address several issues related to user mapping into the container. Ensure the sandbox correctly recognizes various Linux distributions, handles errors gracefully, and securely processes home directory paths.

*   Enhance distribution detection:
    *   Recognize NixOS and Arch Linux as supported distributions.
    *   Parse unquoted, double-quoted, and single-quoted values in the OS identification file (e.g., ID=nixos, ID="nixos", ID='ubuntu').
    *   Use the ID_LIKE field to recognize derivative distributions (e.g., Pop!_OS with ID_LIKE="ubuntu debian").

*   Implement warnings for unrecognized distributions:
    *   Emit a warning via `debugLogger.warn` with the message 'Host UID mismatch detected (current UID: <uid>)' when the host UID is non-zero and the distribution is unrecognized.
    *   Do not emit a warning if the host UID is 0 (root) and the distribution is unrecognized.
    *   Emit a warning via `debugLogger.warn` with the message 'Could not read /etc/os-release' if the OS identification file cannot be read.

*   Improve the user-creation script for the sandbox entrypoint:
    *   Include a check for the availability of user-creation tools using 'if command -v useradd'. If unavailable, print "Error: 'useradd' not found".
    *   Use 'groupadd -g 1000 -o gemini' to create the group and check if UID 1000 already exists with 'id 1000'.
    *   Create the user with 'useradd -o -u 1000' and dynamically resolve the username using 'USER_NAME=$(id -nu 1000 2>/dev/null);'.
    *   Execute 'su -p "$USER_NAME"' if the username is resolved; otherwise, print 'Error: Failed to map host UID 1000' and exit with code 1.

*   Properly quote home directory paths:
    *   Single-quote paths containing spaces or shell-special characters (e.g., '/home/user name `$(id)`' should be "'/home/user name `$(id)`'").

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.