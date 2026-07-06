Implement comprehensive IAM group management functionality in the IAM MCP server. Ensure that operators can list, create, delete, and manage groups, including adding/removing users and attaching/detaching policies. Respect read-only mode by blocking write operations and handle errors consistently.

*   Implement the `IamGroup` model class with fields:
    *   `group_name` (str)
    *   `group_id` (str)
    *   `arn` (str)
    *   `path` (str)
    *   `create_date` (str)

*   Implement the `GroupsListResponse` model class with fields:
    *   `groups` (list of `IamGroup` objects)
    *   `is_truncated` (bool)
    *   `count` (int)
    *   `marker` (optional str)

*   Implement the `GroupDetailsResponse` model class with fields:
    *   `group` (`IamGroup`)
    *   `users` (list of username strings)
    *   `attached_policies` (list of `AttachedPolicy` objects)
    *   `inline_policies` (list of policy name strings)

*   Implement the `CreateGroupResponse` model class with fields:
    *   `group` (`IamGroup`)
    *   `message` (str)

*   Implement the `GroupMembershipResponse` model class with fields:
    *   `message` (str)
    *   `group_name` (str)
    *   `user_name` (str)

*   Implement the `GroupPolicyAttachmentResponse` model class with fields:
    *   `message` (str)
    *   `group_name` (str)
    *   `policy_arn` (str)

*   Implement `list_groups` function:
    *   Accepts `path_prefix` (optional) and `max_items` (default 100).
    *   Returns `GroupsListResponse`.
    *   Raises `IamPermissionError` on AccessDenied.

*   Implement `get_group` function:
    *   Accepts `group_name`.
    *   Returns `GroupDetailsResponse`.
    *   Raises `IamResourceNotFoundError` on NoSuchEntity.

*   Implement `create_group` function:
    *   Accepts `group_name` and `path` (default '/').
    *   Returns `CreateGroupResponse`.
    *   Raises exception with "Cannot create group in read-only mode" in read-only mode.
    *   Raises `IamClientError` on EntityAlreadyExists.

*   Implement `delete_group` function:
    *   Accepts `group_name` and `force` (default False).
    *   Returns a dict with key 'message'.
    *   Raises exception with "Cannot delete group in read-only mode" in read-only mode.
    *   Raises `IamMcpError` on DeleteConflict.

*   Implement `add_user_to_group` function:
    *   Accepts `group_name` and `user_name`.
    *   Returns `GroupMembershipResponse`.
    *   Raises exception with "Cannot add user to group in read-only mode" in read-only mode.
    *   Raises `IamResourceNotFoundError` on NoSuchEntity.

*   Implement `remove_user_from_group` function:
    *   Accepts `group_name` and `user_name`.
    *   Returns `GroupMembershipResponse`.
    *   Raises exception with "Cannot remove user from group in read-only mode" in read-only mode.

*   Implement `attach_group_policy` function:
    *   Accepts `group_name` and `policy_arn`.
    *   Returns `GroupPolicyAttachmentResponse`.
    *   Raises exception with "Cannot attach policy to group in read-only mode" in read-only mode.
    *   Raises `IamValidationError` on InvalidInput.

*   Implement `detach_group_policy` function:
    *   Accepts `group_name` and `policy_arn`.
    *   Returns `GroupPolicyAttachmentResponse`.
    *   Raises exception with "Cannot detach policy from group in read-only mode" in read-only mode.

*   Implement `handle_iam_error` function to map errors:
    *   Map `EntityAlreadyExists` to `IamClientError`.
    *   Map `NoSuchEntity` to `IamResourceNotFoundError`.
    *   Map `DeleteConflict` to `IamMcpError`.
    *   Map `InvalidInput` to `IamValidationError`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.