## Description

The current provisioner logic is a one-size-fits-all implementation, but macOS and Linux hosts require meaningfully different behavior when setting up Docker on remote machines. On macOS, it doesn't make sense to try to automatically download and install Docker because that process is very different — users should be guided to install it manually. On Linux, automatic installation is reasonable. Right now, both platforms share the same code path, so macOS users either get confusing behavior or no useful error when Docker isn't set up correctly.

Additionally, the Docker detection step is missing an important intermediate check: before deciding whether to install Docker, the system should verify whether the container runtime executable is accessible on the system path at all. Without this step, error messages can be confusing and the install path may be triggered when Docker is present but simply not running.

## Expected Behavior

- A separate macOS provisioner should exist that handles the Docker and agent setup flow specifically for macOS remote hosts.
- When the macOS provisioner detects that Docker is not running, it should check whether Docker is installed (accessible on the path) before concluding it needs to be set up. If Docker is not on the path, the provisioner should fail with an error — it should not attempt to install Docker automatically.
- When Docker is running but the required agent process is not active, the provisioner should start the agent automatically.
- The Linux provisioner should also check whether Docker is on the system path before deciding to install it, rather than jumping straight to installation when Docker isn't responding.
- SSH connectivity must be checked before any remote command is attempted, and an error must be returned immediately if the host is unreachable.

## Why This Matters

Splitting provisioners by operating system makes error handling more accurate and user-friendly. macOS users get clear feedback when Docker isn't installed, instead of a failed (and nonsensical) automated install attempt. The intermediate path check improves reliability for both platforms.
