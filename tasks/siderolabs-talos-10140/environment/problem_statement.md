## Description

We want to add integration test support for the OpenEBS container-native storage solution in Talos. As part of this effort, we need to introduce the necessary machine and cluster configuration patches for the test environment and refactor a helper function used across our integration test suite.

Currently, the helper function that retrieves the list of available user disks on a cluster node returns both a disk list and an error value. This two-return pattern means every call site must manually check and handle the error, even though the testing context already provides built-in mechanisms for handling failures. This is inconsistent with how other test suite helpers are structured and adds unnecessary boilerplate to test code.

## Expected Behavior

- The user disk retrieval helper should return only the list of disk paths, with any internal errors handled directly through the test suite's built-in failure mechanism (not returned to callers).
- All existing call sites should be updated to use the simplified single-return-value form.
- Two configuration patch files should be created to support OpenEBS integration test environments:
  - A cluster-level patch that configures pod security admission control to exempt the OpenEBS namespace
  - A machine-level patch that sets required kernel parameters (hugepages), node labels identifying the OpenEBS engine, and extra volume mount configuration for the local storage path

## Why This Matters

The new OpenEBS CSI integration tests will rely on the user disk retrieval helper and benefit from its simplified API. The configuration patches provide the environment prerequisites needed for OpenEBS to run correctly in a QEMU-based test environment with multiple worker nodes and extra disks attached.
