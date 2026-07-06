## Description

KubeVirt incorrectly includes arm64 as a supported architecture for memory hotplug. In practice, memory hotplug is only available for x86_64 virtual machines, not arm64. This means error messages, validation logic, and test coverage all need to be updated to reflect that arm64 is not a supported memory hotplug architecture.

Additionally, CPU hotplug configuration (controlling the maximum number of CPU sockets that can be added dynamically) is being incorrectly applied to arm64 VMs, where it is not supported. CPU hotplug should only be configured for x86_64 and s390x architectures.

The existing test suite uses runtime architecture skip guards to avoid running certain tests on architectures where features are unsupported. This approach silently skips tests rather than explicitly verifying expected behavior per architecture. Tests should instead be parameterized across architectures with explicit architecture-requirement decorators, making multi-architecture support clear and verifiable.

## Expected Behavior

- Memory hotplug must only be supported for x86_64 VMs; the validation error message for unsupported architectures must reflect this (removing arm64 from the list of supported architectures).
- CPU hotplug must not configure maximum sockets for arm64 VMs.
- Memory hotplug must not configure maximum guest memory for arm64 or s390x VMs.
- Architecture-specific tests must use explicit architecture requirement labels rather than skip guards.
- The deprecated skip-on-architecture mechanism for s390x must be removed.
- New test decorators for requiring specific architectures (x86_64, s390x, arm64) must be introduced.

## Why This Matters

Without this fix, arm64 VMs could be incorrectly configured with memory or CPU hotplug settings that are not supported on that architecture, leading to unexpected behavior. Validation messages incorrectly state that arm64 is a supported memory hotplug architecture. Improving multi-architecture test coverage also ensures that future regressions on any architecture are caught explicitly rather than silently skipped.
