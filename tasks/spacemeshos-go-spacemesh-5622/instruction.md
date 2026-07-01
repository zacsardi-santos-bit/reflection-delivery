Refactor the peer-to-peer networking layer in the blockchain node codebase to simplify the request method from a callback-based design to a synchronous call. Update data fetching functions to operate on a single peer, returning results directly, and ensure proper error propagation in downstream operations.

*   Update the `Server.Request` method in `p2p/server/server.go`:
    *   Change the signature to `Request(ctx context.Context, peer peer.ID, request []byte) ([]byte, error)`.
    *   Return `ErrNotConnected` if the peer is unknown.
    *   Return an error for requests exceeding the size limit.
    *   Return the response bytes with a nil error on success.

*   Modify the `Requester` interface in the `fetch` package:
    *   Change the `Request` method signature to `Request(ctx context.Context, peer p2p.Peer, req []byte) ([]byte, error)`.
    *   Remove the previous callback parameters `okFunc` and `errFunc`.

*   Refactor data fetching functions in `fetch/fetch.go`:
    *   `GetMaliciousIDs` should accept a single `p2p.Peer` and return `([]byte, error)`.
    *   `GetLayerData` should accept a single `p2p.Peer` and a `types.LayerID`, returning `([]byte, error)`.
    *   `GetLayerOpinions` should accept a single `p2p.Peer` and a `types.LayerID`, returning `([]byte, error)`.

*   Update `SyntacticallyValidateDeps` in `activation/handler.go`:
    *   Change the signature to `SyntacticallyValidateDeps(ctx context.Context, atx *types.ActivationTx) (*types.VerifiedActivationTx, *types.MalfeasanceProof, error)`.
    *   Ensure the malfeasance proof is nil for valid ATX cases and normal validation failures.

*   Modify `processVerifiedATX` in `activation/handler.go`:
    *   Change the signature to `processVerifiedATX(ctx context.Context, atx *types.VerifiedActivationTx) (*types.MalfeasanceProof, error)`.
    *   Return `(nil, nil)` for valid first-time ATXs and already-stored duplicates.
    *   Return a non-nil `MalfeasanceProof` for duplicate ATXs in the same epoch.

*   Implement error handling for gossip and sync operations:
    *   In `HandleGossipAtx`, publish a `MalfeasanceGossip` message and return `errMaliciousATX` for duplicate ATXs.
    *   In `HandleSyncedAtx`, store the malfeasance proof and return a nil error for duplicate ATXs.

*   Ensure polling functions handle errors correctly:
    *   `PollMaliciousProofs` should call `GetMaliciousIDs` per peer and handle partial failures.
    *   `PollLayerData` should call `GetLayerData` per peer and succeed if at least one peer returns valid data.
    *   `PollLayerOpinions` should call `GetLayerOpinions` per peer and return errors for failures or malformed data.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.