## Description

When a tenant is deleted through the API, there is no background job to clean up all the associated data (cloud providers, scans, resources, findings, etc.). Currently, deleting a tenant leaves orphaned provider data in the database, which is inconsistent and could cause issues with storage and data integrity.

## Expected Behavior

- When a tenant is deleted via the REST API, the system should dispatch a background task that removes the tenant and all its associated cloud providers and their related data.
- The delete endpoint should return a success response immediately after dispatching the background job (it should not wait for all data to be cleaned up synchronously).
- Membership records for the deleted tenant should be removed as part of the deletion process.
- Users who have memberships in other tenants should be preserved — only their membership in the deleted tenant should be removed, not their account.
- When the background cleanup job runs for a tenant with no providers, it should complete successfully and report an empty result.
- When the background cleanup job runs for a tenant with providers, it should delete all providers and return a summary of what was deleted.

## Why This Matters

Without proper tenant cleanup, deleted tenants leave behind orphaned provider data. This wastes storage and makes the database inconsistent. A background task approach ensures the API remains responsive while cleanup happens asynchronously, and proper membership/user handling ensures related accounts are not accidentally deleted.
