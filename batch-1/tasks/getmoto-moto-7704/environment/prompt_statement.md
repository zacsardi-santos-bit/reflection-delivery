I'm working on adding AWS Network Manager support to moto so I can unit test my infrastructure code locally. Right now, moto doesn't mock the Network Manager service at all, so any test that calls into it fails immediately.

I need the mock to support the core operations: creating global networks (which should come back with a pending provisioning state and a properly formatted ARN), creating core networks under a global network (also with a correct ARN), deleting core networks (where the response shows a deletion-in-progress state and the network is gone from subsequent listings), listing all core networks, fetching a specific core network by ID, and describing global networks either all at once or filtered by specific IDs.

I also need tag management to work — both adding tags to existing global networks and core networks (using their ARN), and removing specific tags from them. When trying to tag a resource that doesn't exist, the service should return an error indicating the resource was not found, along with the resource identifier that was used.

The mock should also be accessible through the moto server, specifically the HTTP endpoints for listing global networks and core networks, and the endpoint for adding tags to a resource.
