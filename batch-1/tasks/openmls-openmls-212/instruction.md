Implement the ability for a new member to join a group using a Welcome message that includes an embedded ratchet tree. Ensure that the ratchet tree is extracted and used correctly to construct the new member's group state. Additionally, implement necessary decoding methods to support this functionality.

*   Implement the `new_from_welcome` function in `src/group/mls_group/new_from_welcome.rs`:
    *   When called with a `None` ratchet tree and a Welcome message containing a `RatchetTreeExtension`, extract the ratchet tree from the extension.
    *   Use the extracted ratchet tree to construct the new member's group state and return `Ok(MlsGroup)`.
    *   Ensure the `RatchetTreeExtension` is not removed from the group info's extension list before verifying the group info signature. Re-add it after reading to maintain signature validity.
    *   Ensure this function succeeds when `GroupConfig` is set with `add_ratchet_tree_extension: true`.

*   Implement the `find_key_package_bundle` method on `TestClient` in `tests/utils/mod.rs`:
    *   Accept a reference to a `KeyPackage`.
    *   Search the client's stored key package bundles for a bundle whose key package hash matches the provided key package's hash.
    *   Remove the matching bundle from the collection and return it as `Some(KeyPackageBundle)`.
    *   Return `None` if no matching bundle is found.

*   Implement the `Codec` trait's `decode` method for various components in `src/tree/codec.rs` and `src/tree/node.rs`:
    *   For `NodeType`, decode a `u8` byte from the cursor into a `NodeType` value.
    *   For `Node`, decode `node_type` (as `NodeType`), `key_package` (as `Option<KeyPackage>`), and `node` (as `Option<ParentNode>`) in order.
    *   For `ParentNode`, decode `public_key` (as `HPKEPublicKey`), `unmerged_leaves` (as a vector with `VecU32` size prefix), and `parent_hash` (as a vector with `VecU8` size prefix) in order.

*   Ensure that a Welcome message, once encoded and then decoded, can be used to successfully join the group via `new_from_welcome` with a `None` ratchet tree when the group was configured with `add_ratchet_tree_extension: true`.

*   Ensure that commit messages containing add, remove, and update proposals encode and decode correctly, maintaining equality between the decoded value and the original.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.