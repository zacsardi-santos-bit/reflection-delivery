Implement a read capacity budget mechanism for the DynamoDB migration process to control read capacity consumption. Ensure the migration stops gracefully when the budget is exhausted, recording the status as "incomplete" for future resumption. Update the migration status tracking and handle capacity tracking in paginated reads.

*   Update the `EdgeV2MigrationStatus` enum in `api/projects/models.py`:
    *   Include values: NOT_STARTED, IN_PROGRESS, COMPLETE, and INCOMPLETE.
    *   Ensure `Project.show_edge_identity_overrides_for_feature` returns False for INCOMPLETE.

*   Modify the `Project` model in `api/projects/models.py`:
    *   Use the field `edge_v2_migration_status` instead of `identity_overrides_v2_migration_status`.
    *   Add a nullable field `edge_v2_migration_read_capacity_budget`.
    *   Set `edge_v2_migration_status` to `EdgeV2MigrationStatus.COMPLETE` when an edge-enabled project is created.

*   Define the `CapacityBudgetExceeded` exception in `api/environments/dynamodb/wrappers/exceptions.py`:
    *   Constructor must accept two `Decimal` arguments: `capacity_budget` and `capacity_spent`.
    *   Expose these values via `.capacity_budget` and `.capacity_spent` attributes.

*   Update `DynamoIdentityWrapper` in `api/environments/dynamodb/wrappers/identity_wrapper.py`:
    *   `get_all_items` must accept a `return_consumed_capacity` keyword argument (default False) and include `ReturnConsumedCapacity='TOTAL'` when True.
    *   `iter_all_items_paginated` must:
        *   Accept `capacity_budget` (Decimal or None, default None).
        *   Pass `return_consumed_capacity=True` to `get_all_items` when `capacity_budget` is provided.
        *   Track cumulative `ConsumedCapacity.CapacityUnits` across pages.
        *   Yield items from a page when the cumulative total meets or exceeds `capacity_budget`, but raise `CapacityBudgetExceeded` on the next call.
        *   Accept an `overrides_only` keyword argument (default False).

*   Create `EdgeV2MigrationResult` in `api/environments/dynamodb/types.py`:
    *   Include fields: `status` (EdgeV2MigrationStatus) and `identity_overrides_changeset` (IdentityOverridesV2Changeset).

*   Update `migrate_environments_to_v2` in `api/environments/dynamodb/services.py`:
    *   Accept a `capacity_budget` parameter and pass it to `iter_all_items_paginated`.
    *   Include `projection_expression='environment_api_key, identifier, identity_features, identity_uuid'` and `overrides_only=True`.
    *   Return `EdgeV2MigrationResult` with `status=EdgeV2MigrationStatus.INCOMPLETE` and `identity_overrides_changeset.to_put=[]` on `CapacityBudgetExceeded`.

*   Modify `migrate_project_environments_to_v2` in `api/projects/tasks.py`:
    *   Pass `capacity_budget=Decimal(project.edge_v2_migration_read_capacity_budget)` to `migrate_environments_to_v2`.
    *   Fall back to `Decimal(settings.EDGE_V2_MIGRATION_READ_CAPACITY_BUDGET)` when `edge_v2_migration_read_capacity_budget` is None.
    *   Update `project.edge_v2_migration_status` based on the result's status field when a result is returned.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.