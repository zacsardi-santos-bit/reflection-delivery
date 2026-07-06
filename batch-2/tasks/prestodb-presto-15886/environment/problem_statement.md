## Description

The file-based security access control system currently checks only whether a user has full catalog-level access when determining if they can create, drop, or rename schemas. There is no way for administrators to define finer-grained schema ownership rules — for example, allowing a specific user to own only schemas whose names match a pattern, or blocking all users from managing a protected schema regardless of their catalog access.

We need to extend the file-based security configuration to support a schema ownership rules section. Administrators should be able to specify lists of rules, each matching user identities (by regex pattern) and schema names (by regex pattern), and marking the combination as "owned" or "not owned." These rules should be evaluated in order, with the first matching rule determining the outcome.

## Expected Behavior

- When schema ownership rules are present in the configuration, creating, dropping, or renaming a schema should check whether the requesting user is an owner of that schema according to the rules.
- If no rule matches the user and schema combination, access should be denied.
- If schema rules are absent from the configuration, the existing catalog-level access check should be used as before.
- A rule with an explicit "not owner" flag should block access even for privileged users, as long as it appears before any permissive rule in the list.
- Renaming a schema should check that the user is an owner of both the source and the target schema name.

## Why This Matters

Without schema-level ownership rules, the only security boundary is at the catalog level. This makes it impossible to protect individual schemas within a broadly accessible catalog, or to grant schema management rights to specific users for only a subset of schema names. Adding schema rules enables meaningful multi-tenant schema governance within a single catalog.
