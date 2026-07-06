## Description

On Windows 10 and later, the operating system ships with a built-in SSH client and SSH agent service. However, posh-git does not currently detect or interact with this native SSH infrastructure. As a result, users with the built-in SSH service available get no automatic benefit — they have to manually start the service, configure their git settings, and manage SSH keys themselves each session.

## Expected Behavior

- The shell should be able to detect whether the native Windows OpenSSH SSH agent service is present and available.
- When the native agent is detected, the shell should automatically start the service if it is not already running (provided the user has sufficient privileges).
- If the service is disabled and the user does not have administrative rights, the user should receive a clear error message explaining what needs to be done, and no attempt to start the service should be made.
- If the service is disabled and the user is an administrator, the service should be enabled and then started automatically.
- The git configuration should automatically be updated to point to the native SSH executable, but only if it has not already been configured.
- SSH keys should be added automatically, but only when no keys are already loaded — avoiding duplicate prompts that occur when the native agent is in use.

## Why This Matters

Users on Windows 10+ with the built-in SSH infrastructure currently get a degraded experience because posh-git ignores the native SSH service entirely. By adding awareness of the native service, the shell startup experience becomes seamless for these users without requiring any manual configuration steps.
