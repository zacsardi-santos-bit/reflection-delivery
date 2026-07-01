## Description

When building Debian packages for different versions of Kubernetes, the kubeadm package's dependency list should vary based on the target Kubernetes version. Currently, the dependencies are hardcoded in a template without any version awareness. This is incorrect because starting from Kubernetes 1.11, an additional container runtime tooling package became a required runtime dependency for kubeadm — but there is no code that conditionally includes it based on the target version.

## Expected Behavior

- There should be a function that computes the correct dependency list for the kubeadm package given a target Kubernetes version.
- For versions older than 1.11, the dependency list should include the baseline set: the kubelet package (minimum version 1.6.0), the kubectl package (minimum version 1.6.0), the Kubernetes CNI package (pinned to version 0.6.0), and the standard Debian miscellaneous dependencies.
- For versions 1.11 and newer, the dependency list should additionally include the container runtime interface tools package (minimum version 1.11.0) at the end of the list.
- The dependency entries should be output as a single comma-separated string that can be used directly in a Debian control file.

## Why This Matters

Packaging kubeadm without the correct set of runtime dependencies will result in packages that are missing required tooling at installation time. Getting the dependencies right — and varying them correctly per Kubernetes release — is essential for producing valid, installable Debian packages.
