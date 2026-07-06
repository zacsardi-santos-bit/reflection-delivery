Separate the filtering logic for DDL events in the CDC system to ensure correct handling of tracked tables. Implement distinct methods for discarding and ignoring DDL events based on their scope and user configurations.

*   Update the Filter interface:
    *   Implement `ShouldIgnoreDDLEvent(ddl *model.DDLEvent) (bool, error)` to determine if a DDL event should be ignored.
        *   Return `(true, nil)` if `ddl.StartTs` matches any value in `IgnoreTxnStartTs`.
        *   Return `(true, nil)` if `ddl.Type` matches any event type in the `IgnoreEvent` list for the table.
        *   Return `(true, nil)` if `ddl.Query` matches any SQL pattern in the `IgnoreSQL` list for the table.
        *   Return `(false, nil)` if no ignore conditions are met.
    *   Update `ShouldDiscardDDL(ddlType timodel.ActionType, schema, table string) bool` to determine if a DDL event should be discarded.
        *   Return `true` if the DDL type is not in the allowed list or if the schema/table does not match replication rules.
        *   Return `false` if the DDL type is allowed and the schema/table matches the rules.
        *   Do not consider `startTs` or SQL query content.

*   Modify internal methods:
    *   Change `shouldSkipDDL(ddl *model.DDLEvent) (bool, error)` to accept a full `DDLEvent` struct.
    *   Extract schema, table, type, and query from the `DDLEvent` fields internally.

*   Update the DDL manager:
    *   Modify `newDDLManager` to include a `filter.Filter` parameter between `ddlSink` and `ddlPuller`.
    *   Use the filter to evaluate DDL ignore/discard decisions.

*   Adjust the DDL puller:
    *   Ensure `handleJob` returns `(false, nil)` for `ALTER TABLE` events targeting tables that match filter rules.
    *   Ensure `handleJob` returns `(false, nil)` for jobs targeting in-filter tables, regardless of `StartTS`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.