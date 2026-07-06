## Description

The backfill listing endpoint currently returns all backfills in the system to any authenticated user, regardless of whether that user has permission to view the associated workflows. This is a security gap: users who are restricted from seeing certain workflows can still see backfill operations for those workflows.

## Expected Behavior

- When a user requests the list of backfills, the system should check which workflows the user is authorized to access.
- The endpoint should only return backfills whose associated workflow is permitted for the requesting user.
- The response count should accurately reflect only the authorized backfills, not the total number of backfills in the system.

## Why This Matters

In a multi-tenant or permission-controlled deployment, users are typically restricted to a subset of workflows. The backfill listing endpoint bypasses these restrictions, allowing users to observe activity for workflows they should not have visibility into. Bringing the backfill listing in line with the authorization model ensures consistent access control across the API.
