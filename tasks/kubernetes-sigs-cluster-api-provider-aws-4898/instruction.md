Extend the EKS-managed VPC support to handle multiple secondary CIDR blocks. Implement functions to associate and disassociate these CIDR blocks with the cluster's VPC, ensuring backward compatibility and proper validation.

*   Implement `associateSecondaryCidrs` function in `pkg/cloud/services/network/secondarycidr.go`:
    *   Replace the existing `associateSecondaryCidr` function.
    *   Return nil if `AllSecondaryCidrBlocks()` returns an empty list without making EC2 API calls.
    *   Describe the VPC and compare desired CIDR blocks against existing associations.
    *   Call `AssociateVpcCidrBlock` only for CIDR blocks not already associated.
    *   Return an error if the VPC cannot be described, no VPC is found, or `AssociateVpcCidrBlock` fails.

*   Implement `disassociateSecondaryCidrs` function in `pkg/cloud/services/network/secondarycidr.go`:
    *   Replace the existing `disassociateSecondaryCidr` function.
    *   Return nil if `AllSecondaryCidrBlocks()` returns an empty list without making EC2 API calls.
    *   Describe the VPC and identify currently associated CIDR blocks.
    *   Call `DisassociateVpcCidrBlock` using the association ID for each matched block.
    *   Return an error if the VPC cannot be described, no VPC is found, or `DisassociateVpcCidrBlock` fails.

*   Update `VPCSpec` in `api/v1beta2/network_types.go`:
    *   Add `SecondaryCidrBlocks []VpcCidrBlock` field.
    *   Ensure each `VpcCidrBlock` has an `IPv4CidrBlock` string field.

*   Implement `SecondaryCidrBlocks` and `AllSecondaryCidrBlocks` methods in `pkg/cloud/scope/network.go`:
    *   Add to `NetworkScope` interface.
    *   Implement in `ManagedControlPlaneScope` and `ClusterScope`.
    *   For `ManagedControlPlaneScope`, wrap the legacy `SecondaryCidrBlock` as a single-element list if `SecondaryCidrBlocks` is empty.

*   Add validation in `controlplane/eks/api/v1beta2/awsmanagedcontrolplane_webhook.go`:
    *   During `ValidateCreate` and `ValidateUpdate`, ensure `spec.secondaryCidrBlock` is listed in `spec.network.vpc.secondaryCidrBlocks`.
    *   Return an error with the message: "<cidr> must be listed in AWSManagedControlPlane.spec.network.vpc.secondaryCidrBlocks" if validation fails.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.