Implement a multi-store commitment layer that supports two-level cryptographic proofs and persists commit metadata. Ensure that the system can verify a full proof chain from the leaf level to the overall multi-store commit hash and retrieve historical commit information.

*   Update `CommitInfo` in `store/commit_info.go`:
    *   Implement `GetStoreProof(storeKey string) ([]byte, *CommitmentOp, error)` to return a root hash and a `CommitmentOp` proof. Ensure `CommitmentOp` has a `Run([][]byte) ([][]byte, error)` method that outputs a slice with the first element equal to the overall `CommitInfo` hash.
    *   Ensure `CommitInfo.Hash()` sorts `StoreInfos` lexicographically before computing the hash. After calling `Hash()`, `ci.StoreInfos[0].Name` must be the lexicographically smallest store key.
    *   Implement `GetStoreCommitID(storeKey string) CommitID` to return the `CommitID` for the given store key or an empty `CommitID` if not found.

*   Modify `NewCommitStore` in `store/commitment/store.go`:
    *   Accept a `dbm.DB` parameter between `multiTrees` and `logger`: `NewCommitStore(multiTrees map[string]Tree, db dbm.DB, logger log.Logger) (*CommitStore, error)`.

*   Update the `Tree` interface in `store/commitment/tree.go` and `store/commitment/iavl/tree.go`:
    *   Change the `Commit()` method to return `([]byte, uint64, error)`, including a committed version number. Ensure `IavlTree` starts with version 1 and increments with each commit.

*   Enhance `CommitStore` in `store/commitment/store.go`:
    *   Modify `Commit(version uint64) (*CommitInfo, error)` to accept a version number and return `CommitInfo` with the version and store hashes. Persist this information to the database.
    *   Implement `GetProof(storeKey string, version uint64, key []byte) ([]CommitmentOp, error)` to return a two-element `CommitmentOp` slice for valid versions or `(nil, error)` for pruned/unavailable versions.
    *   Implement `GetCommitInfo(version uint64) (*CommitInfo, error)` to retrieve persisted `CommitInfo` for a given version. Integrate this into the `Committer` interface via `GetStateCommitment().GetCommitInfo(version)`.

*   Update `QueryResult` in `store/store.go`:
    *   Replace `Proof CommitmentOp` with `ProofOps []CommitmentOp`. Ensure `ProofOps[0]` contains the queried key and a `Run` method that returns the tree root hash, and `ProofOps[1]` returns the overall commit root hash.

*   Revise the `Committer` interface in `store/database.go` and `store/commitment/store.go`:
    *   Replace `WorkingStoreInfos(version uint64) []StoreInfo` with `WorkingCommitInfo(version uint64) *CommitInfo`.
    *   Update `Commit()` to `Commit(version uint64) (*CommitInfo, error)`.
    *   Change `GetProof` return type to `([]CommitmentOp, error)`.
    *   Add `GetCommitInfo(version uint64) (*CommitInfo, error)`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.