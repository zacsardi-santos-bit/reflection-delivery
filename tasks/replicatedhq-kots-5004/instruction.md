Implement a feature to include a list of required TCP connections in the node join command response for an embedded cluster. Ensure that the joining node can pre-check connectivity before starting the join process. Refactor the handler to use an injectable client abstraction to facilitate unit testing.

*   Implement the `GetEndpointsToCheck` function in `pkg/embeddedcluster/node_join.go` with the following behavior:
    *   Return an empty slice when no nodes exist in the cluster.
    *   For a joining node without the controller role, return ports 6443 and 9443 for each ready controller node.
    *   For a joining node with the controller role, return ports 6443, 9443, 2380, and 10250 for each ready controller node, and port 10250 for each ready worker node.
    *   Exclude nodes not in the Ready condition (NodeReady condition status is not True).
    *   Determine the controller role name from the embedded cluster Installation custom resource, not from a hardcoded value.
    *   Classify a node as a controller if its 'node-role.kubernetes.io/control-plane' label is set to 'true'.

*   Update the `GetEmbeddedClusterNodeJoinCommandResponse` struct in `pkg/handlers/embedded_cluster_node_join_command.go`:
    *   Add a `TCPConnectionsRequired` field of type `[]string` with JSON key `tcpConnectionsRequired`.
    *   Populate this field using the result from `GetEndpointsToCheck`.

*   Modify the `GetEmbeddedClusterNodeJoinCommand` method in `pkg/handlers/embedded_cluster_node_join_command.go`:
    *   Return HTTP 400 Bad Request if the `EMBEDDED_CLUSTER_ID` environment variable is not set.
    *   Return HTTP 500 Internal Server Error if the store's role lookup returns an error.
    *   Read the 'token' query parameter from the request and pass it to the store's role lookup function.
    *   On a successful response (HTTP 200), create a Kubernetes Secret in the kube-system namespace containing the cluster bootstrap token.
    *   Ensure the `K0sToken` field in the response is a base64-encoded, gzip-compressed kubeconfig with the bootstrap token.

*   Update the `Handler` struct in `pkg/handlers/handlers.go`:
    *   Expose a `KubeClientBuilder` field of type `kubeclient.KubeClientBuilder` to allow test injection of a mock client.

*   Define the `KubeClientBuilder` interface in `pkg/handlers/kubeclient/kubeclient.go`:
    *   Include the method `GetKubeClient(ctx context.Context) (kbclient.Client, error)`.

*   Implement the `MockBuilder` struct in `pkg/handlers/kubeclient/kubeclient.go`:
    *   Include a public `Client` field of type `kbclient.Client`.
    *   Implement `GetKubeClient` to return the `Client`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.