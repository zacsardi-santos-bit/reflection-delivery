## Description

The kubeadm Debian package currently declares outdated minimum version constraints for its dependencies. The minimum versions for the core Kubernetes tooling (the node agent and the CLI) are set to very old releases that are no longer within the supported version window. Additionally, the container runtime tools dependency is pinned to an outdated version and is not consistently included for the minimum currently-supported Kubernetes version.

## Expected Behavior

- The minimum required version for the node agent and CLI packages should match the current minimum supported Kubernetes version, not a much older release.
- The container runtime tools package should declare a minimum version consistent with the current support floor.
- The container runtime tools package dependency should be included for all currently supported Kubernetes versions, including the minimum supported version, not only for versions above some older threshold.
- When looking up which kubeadm configuration file to use, the function should correctly return the current configuration path for all supported versions.

## Why This Matters

Users installing the kubeadm Debian package could technically satisfy the package dependency constraints by installing Kubernetes tooling versions that are years old and completely incompatible. This creates a false sense of correctness during package installation and can result in broken clusters. The declared minimums should reflect the actual supported version window so that package managers enforce a coherent, compatible set of components.
