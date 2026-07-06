I'm working with a Kubernetes cluster API provider for AWS and I need to extend support for secondary CIDR blocks on EKS-managed VPCs. Right now, the provider only lets you specify a single secondary CIDR block. I need it to support a list of secondary CIDR blocks so that multiple additional IP ranges can be associated with the cluster's VPC — which is important for certain pod networking setups.

The association logic should be smart about it: when reconciling, only add CIDR blocks that aren't already associated with the VPC, and skip the ones that are already there. Similarly, when removing a cluster, only disassociate the blocks from the list that are actually associated with the VPC at that time.

On top of the association/disassociation behavior, I also need a validation rule in the admission webhook: if someone uses both the existing single-CIDR field and the new list field at the same time, the single CIDR must appear in the list. If it doesn't, the cluster resource should be rejected with an error that clearly identifies which CIDR is missing and where it needs to be added.

Backward compatibility with the existing single-CIDR field should be maintained — clusters that only use the old field should continue to work as before.
