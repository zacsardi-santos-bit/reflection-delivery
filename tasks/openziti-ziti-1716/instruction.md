Update the `PeerConnected` method in the `controller/raft/mesh/mesh.go` file to return an error value. Ensure that the method can signal both successful and failed peer connection attempts, while maintaining the correct readonly state based on peer version matching.

Requirements:
*   Modify the `PeerConnected` method signature to:
    *   `PeerConnected(peer *Peer) error`
*   Implement error handling in `PeerConnected`:
    *   Return `nil` when a peer is successfully connected with no existing connection for its address.
    *   Return an appropriate error when a connection attempt is made for a peer address that already has an existing connection.
*   Maintain the mesh's readonly state logic:
    *   If a peer with a matching version connects, ensure the readonly state remains `false`.
    *   If a peer with a non-matching version connects, set the readonly state to `true`. The method should still return `nil` in this case, as the version mismatch affects readonly mode but is not an error in the connection itself.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.