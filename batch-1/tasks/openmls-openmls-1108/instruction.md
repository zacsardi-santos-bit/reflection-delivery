Implement an enhanced validation rule for determining when a commit requires an update path in an MLS protocol. Ensure that each proposal type explicitly declares its path requirement and update the commit validation and generation logic accordingly.

*   Update the `Proposal` enum:
    *   Implement a public method `is_path_required(&self) -> bool` in `openmls/src/messages/proposals.rs`.
        *   Return `false` for Add, PreSharedKey, ReInit, and AppAck variants.
        *   Return `true` for Update, Remove, ExternalInit, and GroupContextExtensions variants.

*   Modify commit path requirements:
    *   Determine if a commit requires a path by checking if any proposal in the commit returns `true` from `is_path_required()`.
    *   Require a path for external commits and empty commits (implicit self-update).
    *   Ensure a path is not required when all proposals individually do not require a path.
    *   Ensure `Commit::has_path()` returns `true` only if the commit contains a path.

*   Handle commit processing:
    *   Fail processing of a commit without a path if its proposals require a path, using `UnverifiedMessageError::InvalidCommit(StageCommitError::RequiredPathNotFound)`.
    *   Successfully process a commit without a path if its proposals do not require a path.

*   Enhance `MlsGroup` functionality:
    *   Make `proposal_store` field `pub(crate)` in `openmls/src/group/mls_group/mod.rs`.
    *   Make `framing_parameters()` method `pub(crate)` accessible.
    *   Implement `store_pending_proposal(&mut self, proposal: QueuedProposal)` to store a `QueuedProposal`.
    *   Implement `clear_pending_proposals(&mut self)` to clear all pending proposals.

*   Support proposal queuing:
    *   Implement `QueuedProposal::from_proposal_and_sender` constructor in `openmls/src/group/proposals.rs`.
        *   Accept a ciphersuite, backend, proposal, and sender reference.

*   Enable serialization and deserialization:
    *   Implement `serde::Serialize` and `serde::Deserialize` (or `Into<SerializedMlsGroup>` / `From<SerializedMlsGroup>`) for `MlsGroup` using `SerializedMlsGroup` in `openmls/src/group/mls_group/ser.rs`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.