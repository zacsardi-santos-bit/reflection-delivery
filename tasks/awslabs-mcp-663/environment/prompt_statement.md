I'm working on an IAM management server that already supports users and roles, but it has no support for groups at all. I need to add a complete set of group management operations so that operators can list groups, retrieve detailed group information (including members and attached policies), create and delete groups, add and remove users from groups, and attach and detach policies to groups.

For write operations, the server already has a read-only mode that blocks changes — the new group operations should respect that the same way user operations do, raising a clear error when someone tries to create, delete, or modify groups in read-only mode.

Error handling also needs to be consistent with the rest of the server: when a group or user is not found it should raise a not-found error, when a group can't be deleted because it still has members or policies it should raise a conflict error, and when a policy isn't attachable it should raise a validation error.

For the delete operation specifically, there should be a force option that automatically removes all members, detaches all managed policies, and deletes all inline policies before deleting the group itself — so operators don't have to clean everything up manually first.
