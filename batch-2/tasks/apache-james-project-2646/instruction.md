Implement the correct handling of ACL rights for mailbox rename and move operations in a mail server. Ensure that users with appropriate permissions can perform these operations, and provide clear error messages when permissions are lacking.

*   Update `MailboxManager.renameMailbox` to enforce ACL rights:
    *   Throw `InsufficientRightsException` instead of `MailboxNotFoundException` when a user lacks the necessary rights.
    *   Allow renaming when the user has the `DeleteMailbox` right on the source mailbox.
    *   Allow renaming to a child path when the user has the `CreateMailbox` right on the destination parent.
    *   Return a `List<MailboxRenamedResult>` with correct `getOriginPath()` and `getDestinationPath()` values for successful operations.

*   Modify JMAP `Mailbox/set` operations:
    *   When renaming a mailbox without `DeleteMailbox` right, include the mailbox ID in `notUpdated` with type 'forbidden' and description 'Invalid change to a delegated mailbox'.
    *   Allow renaming when the user has `CreateMailbox` right on the parent and sufficient rights on the child mailbox, reflecting changes in the `updated` map.
    *   Ensure re-parenting succeeds when the user has `DeleteMailbox` right on the source and `CreateMailbox` right on the destination parent, updating the `updated` map.
    *   Return 'forbidden' error type in `notUpdated` responses for unauthorized operations.

*   Implement the `InsufficientRightsException` in `mailbox/api/src/main/java/org/apache/james/mailbox/exception/InsufficientRightsException.java` to be thrown by `renameMailbox` when rights are insufficient.

*   Ensure `MailboxRenamedResult` in `mailbox/api/src/main/java/org/apache/james/mailbox/` provides:
    *   `getOriginPath()` returning the original `MailboxPath`.
    *   `getDestinationPath()` returning the new `MailboxPath`.

*   Adjust the error type for unauthorized operations in JMAP to "forbidden" with the description "Invalid change to a delegated mailbox".

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.