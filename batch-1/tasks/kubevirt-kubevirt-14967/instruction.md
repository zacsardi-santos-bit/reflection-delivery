Update the KubeVirt codebase to correctly handle architecture support for memory and CPU hotplug features, ensuring accurate validation and test coverage. Implement architecture-specific decorators for test cases and remove deprecated skip mechanisms.

*   Ensure memory hotplug is only supported for x86_64 VMs:
    *   Update validation error message to: 'Memory hotplug is only available for x86_64 VMs'.
    *   Do not configure memory hotplug (MaxGuest) for arm64 or s390x VMs.
*   Ensure CPU hotplug configuration is correctly applied:
    *   Do not set MaxSockets for arm64 VMs, even if MaxCpuSockets is specified.
    *   Apply MaxSockets configuration for x86_64 and s390x VMs.
*   Update validation logic:
    *   For ARM64 CPU thread violations, use Type=CauseTypeFieldValueInvalid, Field='fake.architecture', and Message='threads must not be greater than 1 at fake.domain.cpu.threads (got 2) when fake.architecture is arm64'.
    *   For DedicatedCPUPlacement thread violations, use Type=CauseTypeFieldValueInvalid, Field='fake.domain.cpu.dedicatedCpuPlacement', and ensure the message contains 'Not more than two threads must be provided at fake.domain.cpu.threads (got 3) when DedicatedCPUPlacement is true'.
*   Refactor test suite:
    *   Introduce new test label decorators in `tests/decorators/decorators.go`:
        *   RequiresAMD64 = Label("requires-amd64")
        *   RequiresS390X = Label("requires-s390x")
        *   RequiresARM64 = Label("requires-arm64")
    *   Remove SkipIfS390X function from `tests/framework/checks/skips.go`.
    *   Parameterize tests per architecture:
        *   For unsupported machine type scheduling:
            *   Use 'pc-q35-test-1.2.3' with RequiresAMD64 for amd64.
            *   Use 'virt-test-1.2.3' with RequiresARM64 for arm64.
            *   Use 's390-ccw-virtio-test-1.2.3' with RequiresS390X for s390x.
        *   For panic device causing VMI failure:
            *   Use Isa panic device model with RequiresAMD64 for amd64.
            *   Use Pvpanic panic device model with RequiresARM64 for arm64.
    *   Update memory live update test for 'architecture is not amd64' to use 'arm64' as the test architecture.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.