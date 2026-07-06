## Description

When Docker builds container images from remote Git repositories, it clones those repositories and also initializes any submodules they contain. Currently, submodules are allowed to reference local file system paths on the machine performing the build. This is a security concern: a maliciously crafted repository could include a submodule pointing to a sensitive directory on the Docker host, potentially exposing its contents during a build.

Additionally, the internal git helper code is currently structured as standalone utility functions that don't carry any context about the operation being performed. This makes it difficult to extend behavior on a per-clone basis (for example, isolating system or user git configuration when performing builds on behalf of arbitrary users).

## Expected Behavior

- The system should prevent git operations from using the local file protocol as a transport, so submodule sources cannot reference paths on the host filesystem.
- The core git operations (cloning, fetching, checking out) should be methods on the repository configuration rather than standalone functions, so context about the current operation can be carried through the call chain.
- Submodules must continue to work correctly when their sources are accessible via network protocols (such as HTTP).

## Why This Matters

Without this change, a repository with a crafted submodule configuration could be used to read sensitive data off the build host during a Docker build. Hardening the git transport options eliminates this attack surface. Refactoring the helpers to be method-based also lays groundwork for future configuration options such as isolating git configuration to prevent user or system settings from interfering with builds.
