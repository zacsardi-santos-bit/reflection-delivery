## Description

The Terraform provider for Azure has Arc-enabled Kubernetes cluster resources that interact with Azure's Hybrid Kubernetes service. These resources are currently using an outdated API version for that service, which has become incompatible with the rest of the codebase after a dependency was updated. The old API version is referenced in the vendor directory, the module configuration, and several source files. As a result, the provider fails to compile.

## Expected Behavior

- The Arc-enabled Kubernetes cluster resources (the main cluster resource, the extension resource, the flux configuration resource, and the client) should all use the current API version for the Hybrid Kubernetes connected clusters service.
- The vendored SDK package for the Hybrid Kubernetes service should be updated from the old API version to the current one, replacing all references to the old version throughout.
- The vendor manifest file should reflect the updated package path.
- Once compilation is restored, the cluster's distribution attribute should correctly reflect the value returned by the updated API.

## Why This Matters

Until this is fixed, the entire arckubernetes service package fails to compile, making it impossible to run any tests — including unrelated validation tests in the same package tree. Updating to the current API version restores build integrity and ensures the provider accurately reports resource attributes as the Azure service returns them.
