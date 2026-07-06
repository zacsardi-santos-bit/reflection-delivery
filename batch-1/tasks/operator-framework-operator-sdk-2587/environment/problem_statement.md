## Description

The Ansible operator watches system currently only supports loading roles by specifying a traditional directory path or by using a roles path environment variable. However, modern Ansible workflows encourage packaging and distributing roles as part of Ansible collections, referenced using a three-part dotted naming convention (namespace, collection, and role name). There is currently no way to reference a role installed as part of an Ansible collection in the watches configuration — you have to know and hardcode the full filesystem path.

## Expected Behavior

- Operators should be able to reference an Ansible role in their watches configuration using the standard fully-qualified collection name format (three dot-separated segments).
- When the watches file is loaded, the system should search for the role in the standard Ansible collection installation directories (both the system-wide location and the current user's home directory).
- A configurable environment variable should allow overriding which collection directories are searched, similar to how the roles path environment variable works for traditional roles.
- If the watches configuration references a collection role that cannot be found in any of the expected locations, loading should fail with an error rather than silently accepting an invalid configuration.
- When the collection path environment variable is set, only those specified locations should be searched (not the system/user defaults).

## Why This Matters

Without this support, operators that want to use roles distributed as Ansible collections are forced to hardcode filesystem paths, which breaks portability and goes against Ansible best practices. This change brings the operator watches system in line with how modern Ansible roles are organized and referenced.
