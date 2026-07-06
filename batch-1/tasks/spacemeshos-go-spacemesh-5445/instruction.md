Implement the necessary changes to ensure the mesh service accurately reflects the verified block per layer and excludes ATX activation events from account mesh data. Update the relevant methods and interfaces to support these requirements.

*   Implement the `GetLayerVerified` method in `mesh/mesh.go`:
    *   Signature: `GetLayerVerified(tid types.LayerID) (*types.Block, error)`
    *   Return the single applied block for a given layer.
    *   Return `(nil, nil)` if no blocks exist or none is marked as applied.
    *   Return a pointer to the applied block with correct `LayerIndex` and `ID` if set via `layers.SetApplied`.

*   Update the `MeshAPI` interface in the `grpcserver` package:
    *   Include the `GetLayerVerified` method with the signature `GetLayerVerified(tid types.LayerID) (*types.Block, error)`.
    *   Ensure `MeshAPIMock` implements this method, returning `block1` in the standard mock.

*   Modify the `readLayer` method in `api/grpcserver/mesh_service.go`:
    *   Signature: `readLayer(ctx context.Context, lid types.LayerID, status pb.Layer_LayerStatus) (*pb.Layer, error)`
    *   Call `GetLayerVerified` and handle errors by returning an error message containing "error reading layer data".
    *   If `GetMeshTransactions` reports missing transactions, return an error with "error retrieving tx data".
    *   Allow `readLayer` to succeed if `GetLayerStateRoot` returns an error, as this is non-fatal.
    *   Return successfully without error if `GetLayerVerified` returns nil (no verified block).

*   Ensure `LayersStream` returns:
    *   Exactly one effective block per layer (the verified/applied block).
    *   Zero ATXs.
    *   Layer transaction IDs that match the applied block's transaction IDs, order-independent.

*   Update account mesh data queries and streams:
    *   Exclude ATX activation events.
    *   Return zero results for activations-only queries.
    *   Return only transaction data for all account mesh data queries.

*   Modify `GetMeshTransactions` to include:
    *   The transaction state (`types.APPLIED`) in returned `MeshTransaction` objects.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.