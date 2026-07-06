Create a new macOS-specific provisioner package to handle Docker setup on macOS remote hosts differently from Linux. Implement a check to ensure Docker is on the system path before attempting any installation, and update the existing Linux provisioner to include this check as well.

*   Create a new package for macOS:
    *   Location: `provisioner/darwin`
    *   Go package name: `darwin`
    *   Define a `Docker` struct with a `sshClient` field of type `ssh.Clienter`.
    *   Implement the `New` function to return a pointer to a `Docker` struct:
        *   Signature: `New(sshClient ssh.Clienter) *Docker`

*   Implement the `Provision` method for the darwin `Docker` type:
    *   Signature: `(d *Docker) Provision(ctx context.Context, isRemote bool) error`
    *   Check if Docker is running using a `docker_version` script.
    *   If Docker is not running, check if it is on the PATH using a `has_docker` script.
    *   Return an error if Docker is not found on the PATH, without attempting installation.
    *   Check if the fx-agent container is running using a `check_fx_agent` script.
    *   Start the agent using a `start_fx_agent` script if it is not running.

*   Implement the `runCmd` method for the darwin `Docker` type:
    *   Signature: `(d *Docker) runCmd(script string, isRemote bool, options ...ssh.CommandOptions) error`
    *   Verify SSH connectivity using `Connectable(sshConnectionTimeout)` when `isRemote` is true.
    *   Return an error if SSH connectivity fails.
    *   Execute the command via `RunCommand` when `isRemote` is true.
    *   Execute the command as a local process when `isRemote` is false.

*   Define a `scripts` map in the darwin package:
    *   Type: `map[string]string`
    *   Include keys: `docker_version`, `has_docker`, `check_fx_agent`, `start_fx_agent`.

*   Define a `sshConnectionTimeout` constant in the darwin package:
    *   Type: `time.Duration`
    *   Value: `10 * time.Second`

*   Ensure the darwin provisioner satisfies the `provisioner.Provisioner` interface.

*   Update the existing Linux provisioner:
    *   Location: `provisioner/linux`
    *   Go package name: `linux`
    *   Add a `has_docker` key to the `scripts` map.
    *   Update the `Provision` method:
        *   Signature: `(d *Docker) Provision(ctx context.Context, isRemote bool) error`
        *   Check `has_docker` after a failed `docker_version` check.
        *   Only run `install_docker` if both `docker_version` and `has_docker` checks fail.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.