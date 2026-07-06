## Description

The IAM MCP server currently supports managing individual users and roles but has no support for IAM groups. This is a significant gap because groups are a fundamental way to manage permissions at scale in AWS — you assign policies to groups and then add users to those groups rather than managing permissions user by user.

Without group support, operators cannot programmatically list groups, create or delete groups, manage group memberships (adding or removing users), or attach and detach policies from groups through the server.

## Expected Behavior

- It should be possible to list all groups in the account, with optional filtering by path prefix
- It should be possible to retrieve detailed group information including its current members and attached policies
- It should be possible to create and delete groups, with force-deletion support that automatically cleans up members and policies first
- It should be possible to add and remove users from groups
- It should be possible to attach and detach managed policies to/from groups
- All write operations (create, delete, add user, remove user, attach policy, detach policy) must be blocked when the server is running in read-only mode
- Errors must be properly categorized: not-found errors when a group or user does not exist, conflict errors when a group cannot be deleted because it still has dependencies, and validation errors when a policy cannot be attached

## Why This Matters

Group management is essential for operating AWS accounts with more than a handful of users. Without it, the server cannot support common workflows like onboarding a user to a team group or auditing group memberships. Adding full group lifecycle support brings the server's capabilities to the same level it already provides for users and roles.
