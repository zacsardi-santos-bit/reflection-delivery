Implement a background task to handle the deletion of a tenant and its associated data when a tenant is deleted via the REST API. Ensure the API remains responsive by immediately returning a success response while the cleanup occurs asynchronously.

*   Implement the `delete_tenant` function in `api/src/backend/tasks/jobs/deletion.py`.
    *   Accept a single string parameter `pk` (the tenant's primary key).
    *   Delete the tenant and all associated providers from the database using `MainRouter.admin_db`.
    *   Return an empty dict `{}` if the tenant has no providers.
    *   Return a non-None dict summarizing deleted objects if providers exist.
*   Implement a Celery task `delete_tenant_task` in `api/src/backend/tasks/tasks.py`.
    *   Decorate with `@shared_task(name="tenant-deletion")`.
    *   Accept a `tenant_id` string parameter.
    *   Call `delete_tenant(pk=tenant_id)`.
    *   Import `delete_tenant_task` into `api/src/backend/api/v1/views.py`.
*   Update the tenant delete view in `api/src/backend/api/v1/views.py`.
    *   Dispatch the `delete_tenant_task` using `delete_tenant_task.apply_async(kwargs={"tenant_id": tenant_id})`.
    *   Return HTTP 204 No Content immediately after dispatching the task.
*   Ensure all membership records for the deleted tenant are removed.
    *   Preserve users with memberships in other tenants by only removing their membership in the deleted tenant.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.