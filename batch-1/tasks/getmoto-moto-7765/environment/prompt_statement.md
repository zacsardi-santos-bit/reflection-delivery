I'm trying to write unit tests for code that creates and manages SageMaker HyperPod Clusters, but the AWS mock doesn't support any of the cluster APIs yet. When I try to use the mock, every cluster-related call fails because there's no implementation behind it.

I need the mock to support the full lifecycle of a cluster: creating one with instance groups, VPC settings, and tags; looking it up by name or ARN; listing all clusters with optional name filtering and sorting; deleting a cluster; and seeing a proper not-found error when trying to describe one that no longer exists.

I also need to be able to inspect individual nodes inside a cluster — both looking up a specific node by its identifier and listing all nodes for a cluster, optionally filtered by instance group name. Each node should show its status, instance type, lifecycle configuration, and how many threads per core it uses.

Tagging should work end-to-end: tags passed at creation time and tags added later should both show up when I call the list-tags operation on the cluster ARN.

The mock should also enforce the same validation rules that real AWS does — if I try to create two clusters with the same name, I should get a resource-already-exists error, and if a lifecycle configuration's source storage path doesn't start with the expected prefix, I should get a validation error.

Finally, clusters created with tags should show up correctly when I query the Resource Groups Tagging API filtered to the SageMaker service.
