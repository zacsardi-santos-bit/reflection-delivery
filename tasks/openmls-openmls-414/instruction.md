Implement a configuration option in the managed group to control the inclusion of the ratchet tree in Welcome messages. Ensure that new members can join using only the Welcome message when the option is enabled, and handle errors appropriately when it is disabled.

*   Update the `ManagedGroupConfig` constructor:
    *   Modify `ManagedGroupConfig::new` to accept a boolean parameter `use_ratchet_tree_extension` as its fifth argument.
    *   Ensure the signature is: `ManagedGroupConfig::new(handshake_message_format, update_policy, max_past_epochs, number_of_resumption_secrets, use_ratchet_tree_extension: bool, callbacks) -> ManagedGroupConfig`.
    *   Implement logic to include the ratchet tree in Welcome messages when `use_ratchet_tree_extension` is true.

*   Modify the `ManagedGroup::new_from_welcome` function:
    *   Ensure it succeeds (returns `Ok`) when called with a Welcome message containing the ratchet tree extension and `None` as the ratchet tree argument.
    *   Ensure it returns `Err(ManagedGroupError::Group(GroupError::WelcomeError(WelcomeError::MissingRatchetTree)))` when called with a Welcome message that does not contain the ratchet tree extension and `None` as the ratchet tree argument.

*   Update error handling:
    *   Add a `MissingRatchetTree` variant to the `WelcomeError` enum in the appropriate module (`src/messages/errors.rs`).
    *   Use this error variant when a join attempt fails due to a missing ratchet tree in the Welcome message and no external ratchet tree is provided.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.