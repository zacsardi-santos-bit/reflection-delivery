Implement the functionality to evaluate and manage encryption key actions based on changes in community or channel states. Ensure that the system can compare two states of a community and determine the necessary cryptographic actions required when permissions or memberships change.

*   Implement the `AddMemberToChat` method on the `Community` struct:
    *   Accept parameters: `chatID` (string), `publicKey` (*ecdsa.PublicKey), `roles` ([]protobuf.CommunityMember_Roles).
    *   Add the member to the specified chat in the community's description.
    *   Return the resulting `CommunityChanges`.
    *   Return an authorization error if the caller is not an owner or admin.
    *   Return an error if the chat does not exist.

*   Implement the `EvaluateCommunityEncryptionKeyActions` function:
    *   Accept two `Community` pointers: `origin` (original state) and `modified` (modified state).
    *   Return a pointer to an `EncryptionKeyActions` struct with:
        *   `CommunityKeyAction` field of type `EncryptionKeyAction`.
        *   `ChannelKeysActions` field, a map from chat ID string to `EncryptionKeyAction`.

*   Ensure the following behaviors for `EvaluateCommunityEncryptionKeyActions`:
    *   Return `CommunityKeyAction` with `ActionType` as `EncryptionKeyNone` and an empty `ChannelKeysActions` map if no differences exist between `origin` and `modified` states.
    *   At the community level:
        *   Set `ActionType` to `EncryptionKeyAdd` when a `BECOME_MEMBER` token permission is added to a previously non-token-gated community.
        *   Set `ActionType` to `EncryptionKeyRemove` when all `BECOME_MEMBER` permissions are removed.
        *   Set `ActionType` to `EncryptionKeyNone` for specified conditions involving `CAN_VIEW_CHANNEL` and `BECOME_MEMBER` permissions.
        *   Set `ActionType` to `EncryptionKeySendToMembers` when a member is added to a token-gated community, including only the new members in `Members`.
        *   Set `ActionType` to `EncryptionKeyRekey` when members are removed, including remaining members in `Members`.
        *   Handle simultaneous permission and member changes, prioritizing permission changes.
    *   At the channel level:
        *   Add or remove `CAN_VIEW_CHANNEL` permissions, setting `ActionType` to `EncryptionKeyAdd` or `EncryptionKeyRemove` respectively.
        *   Handle member additions/removals in token-gated channels, setting `ActionType` to `EncryptionKeySendToMembers` or `EncryptionKeyRekey` accordingly.
        *   Set `ActionType` to `EncryptionKeyNone` for member changes in open channels.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.