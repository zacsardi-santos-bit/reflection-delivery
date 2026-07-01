Implement functions to support the native Windows SSH agent in posh-git. Detect the presence of the native SSH service, manage its startup, and configure git settings and SSH keys automatically.

*   Implement `Get-NativeSshAgent()` in `src/GitUtils.ps1`:
    *   Return a non-null service object with a Name property equal to 'ssh-agent' if the Windows native OpenSSH ssh-agent service is present and the ssh.exe binary's version contains 'OpenSSH'.
    *   Return $null if the native OpenSSH setup is not found or not running on Windows.

*   Implement `Start-NativeSshAgent([switch]$Quiet, [string]$StartupType = 'Manual')` in `src/GitUtils.ps1`:
    *   Return $true on success or if the service is disabled and the user is not an administrator.
    *   Return $false if `Get-NativeSshAgent` returns nothing.
    *   If the service StartType is "Disabled":
        *   If the user is an administrator, call `Set-Service "ssh-agent"` with `-StartupType` set to $StartupType (default "Manual"), then start the service.
        *   If the user is not an administrator, write an error message: "The ssh-agent service is disabled. Please start the service and try again." Do not start the service but return $true.
    *   If the service Status is not "Running", call `Start-Service "ssh-agent"`.
    *   Configure git's global `core.sshCommand` to the path of ssh.exe (with backslashes converted to forward slashes) only if it is not already set in the global git config.
    *   Add SSH keys by invoking `ssh-add` only if no keys are currently loaded (determine by running `ssh-add -L` and checking exit code: exit code 0 means keys present, skip; non-zero means no keys, add them).

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.