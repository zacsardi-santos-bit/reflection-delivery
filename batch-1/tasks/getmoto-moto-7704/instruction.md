Implement a mock AWS Network Manager service in the moto library to enable local testing of infrastructure code without real AWS credentials. Ensure the mock supports operations for creating, managing, and tagging global and core networks, and is accessible via the moto server.

*   Create a new moto service module at `moto/networkmanager/` with the following files:
    *   `__init__.py`: Export `networkmanager_backends`.
    *   `models.py`: Implement `NetworkManagerBackend`, `GlobalNetwork`, and `CoreNetwork` classes.
    *   `responses.py`: Implement `NetworkManagerResponse` class for handling HTTP requests.
    *   `urls.py`: Define URL routing for the service.
    *   `exceptions.py`: Define `ResourceNotFound` exception.

*   Implement `NetworkManagerBackend` class in `models.py`:
    *   `create_global_network(description, tags) -> GlobalNetwork`: Return a global network with 'PENDING' state and a formatted ARN.
    *   `create_core_network(global_network_id, description, tags, policy_document, client_token) -> CoreNetwork`: Return a core network with a formatted ARN.
    *   `delete_core_network(core_network_id) -> CoreNetwork`: Return the core network with 'DELETING' state and ensure it is removed from listings.
    *   `list_core_networks() -> List[CoreNetwork]`: Return a list of all core networks.
    *   `get_core_network(core_network_id) -> CoreNetwork`: Return a specific core network by ID.
    *   `describe_global_networks(global_network_ids) -> List[GlobalNetwork]`: Return global networks, filtered by IDs if provided.
    *   `tag_resource(resource_arn, tags) -> None`: Append tags to the resource identified by ARN.
    *   `untag_resource(resource_arn, tag_keys) -> None`: Remove specified tags from the resource identified by ARN.

*   Implement `GlobalNetwork` and `CoreNetwork` classes in `models.py`:
    *   `GlobalNetwork.to_dict()`: Return a dictionary with keys: GlobalNetworkId, GlobalNetworkArn, Description, Tags, State, CreatedAt.
    *   `CoreNetwork.to_dict()`: Return a dictionary with keys: CoreNetworkId, CoreNetworkArn, GlobalNetworkId, Description, Tags, PolicyDocument, State, CreatedAt.

*   Implement `NetworkManagerResponse` class in `responses.py`:
    *   Handle HTTP operations: `create_global_network`, `create_core_network`, `delete_core_network`, `list_core_networks`, `get_core_network`, `describe_global_networks`, `tag_resource`, `untag_resource`.

*   Define URL routing in `urls.py`:
    *   Include paths: `/global-networks`, `/core-networks`, `/core-networks/{id}`, `/tags/{resource-arn}`.

*   Implement `ResourceNotFound` exception in `exceptions.py`:
    *   Return a JSON error with "Message": "Resource not found." and "ResourceId": <provided identifier>.

*   Register the service in `moto/backend_index.py` under the URL pattern for `networkmanager.amazonaws.com`.

*   Ensure the moto server exposes:
    *   GET `/global-networks`: Return a JSON object with 'GlobalNetworks'.
    *   GET `/core-networks`: Return a JSON object with 'CoreNetworks'.
    *   POST `/tags/{resource-id}` for unknown resources: Return a JSON error with 'Message': 'Resource not found.' and 'ResourceId'.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.