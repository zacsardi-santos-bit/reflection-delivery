## Description

When a user updates an ArgoCD project, the system currently does not verify whether the caller has permission to affect the specific clusters and repositories that the project change involves. This is a security gap: a user with only project-update access can unilaterally remove cluster destinations from a project or revoke repository access — effectively changing which infrastructure resources are accessible to all applications in that project — without needing any cluster-level or repository-level permissions.

Similarly, modifying which resource types are globally allowed or blocked for a project should also require cluster-level update permissions for the affected clusters, since these settings directly govern what can be deployed across all destination clusters.

## Expected Behavior

- Removing a cluster from a project's allowed destinations should require the caller to have permission to update that specific cluster. Users lacking this permission should be rejected.
- Removing a repository from a project's allowed source repositories should require the caller to have permission to update that specific repository. Users lacking this permission should be rejected.
- Changing the cluster-scoped resource allowlist or namespace-scoped resource blocklist for a project should require cluster update permissions for each destination cluster. Users lacking this permission should be rejected.
- Users who only have permission to update projects should not be able to perform any of these operations without the additional required permissions.

## Why This Matters

Without these checks, the project update endpoint effectively bypasses the cluster and repository permission model. A restricted user could use project updates to silently revoke cluster or repository access for an entire project, affecting all applications under it.
