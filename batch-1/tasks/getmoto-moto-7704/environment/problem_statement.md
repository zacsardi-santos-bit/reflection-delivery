## Description

The moto library currently has no support for the AWS Network Manager service. This means developers who write code that creates and manages global networks and core networks cannot test their code locally — any unit test that touches Network Manager APIs will fail immediately without real AWS credentials and connectivity.

## Expected Behavior

Moto should provide a mock implementation of the AWS Network Manager service that supports:

- Creating global networks with a description and tags; newly created global networks should have a pending/provisioning state and a proper ARN
- Creating core networks associated with a global network, with description, tags, a policy document, and a client token; core networks should also have a proper ARN referencing the account
- Deleting a core network — the response should reflect a deletion-in-progress state, and the network should no longer appear in listing operations
- Listing all core networks
- Retrieving a specific core network by its ID
- Describing global networks, either all of them or filtered by one or more IDs
- Adding tags to both global networks and core networks using their ARN
- Removing specific tags from both global networks and core networks using their ARN
- When tagging a resource that does not exist, returning an appropriate error indicating the resource was not found, along with the resource identifier

Additionally, the mock should be accessible via the moto server (i.e., the HTTP endpoints for listing global networks, listing core networks, and tagging resources should work correctly).

## Why This Matters

Without this support, teams that use moto for testing infrastructure automation code are blocked from writing meaningful tests for anything that interacts with AWS Network Manager. Adding this mock unlocks local, offline, credential-free testing for a whole class of networking infrastructure code.
