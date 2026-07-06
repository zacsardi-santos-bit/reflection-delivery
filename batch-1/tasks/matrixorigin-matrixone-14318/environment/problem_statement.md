## Description

Several packages in the codebase are failing to compile because they rely on UUID generation functions that have been removed from the newer version of the UUID library. When the library was upgraded, some previously available functions were removed or significantly changed. This breaks compilation across multiple packages — causing hundreds of tests to fail even though those tests have nothing to do with unique ID generation, simply because the affected packages cannot be compiled at all.

Beyond the compilation breakage, there is a deeper correctness issue: one of the old UUID generation approaches produced time-based identifiers that embed the machine's MAC address into the identifier bytes. In Docker and containerized environments, this causes test assertions to fail because the embedded node bytes can match Docker-specific network interface prefixes — something the system explicitly must avoid.

## Expected Behavior

- The UUID library dependency must be upgraded to a version that supports the time-ordered UUID format (v7), and all usages of the removed or deprecated generation functions must be replaced with the new time-ordered generator.
- All unique identifier generation throughout the codebase — including for internal services (compute nodes, transaction nodes, log services), pipeline UUIDs, session identifiers, computation wrappers, segment and object IDs, transaction IDs, and test cluster configurations — should use the modern time-ordered UUID format.
- Generated identifiers must not embed MAC address bytes, so their node section does not match Docker network prefix patterns.
- After migration, all previously failing packages must compile and their full test suites must pass.

## Why This Matters

The compilation failure caused by the removed function means that hundreds of unrelated tests (privilege checks, session handling, protocol handling, SQL compilation, etc.) fail even though they have nothing to do with UUID generation. Migrating to the newer UUID format both restores compilation and ensures that identifier generation is safe for containerized deployments where MAC address embedding causes unpredictable failures.
