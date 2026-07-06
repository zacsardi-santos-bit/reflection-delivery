Implement a robust mechanism for handling online index operations during SQL Server schema migrations. Update the SQL generation logic to use a TRY/CATCH approach for online index operations, and correct the edition detection logic to properly recognize supported editions.

*   Modify `AddIndexTask.java` and `DropIndexTask.java`:
    *   Ensure SQL generation for online index creation and dropping on SQL Server (MSSQL_2012) uses a TRY/CATCH block.
    *   Use the format: 
        ```
        BEGIN TRY 
            EXEC('<create/drop index sql> WITH (ONLINE = ON)');
        END TRY 
        BEGIN CATCH 
            <create/drop index sql>; 
        END CATCH;
        ```
    *   Replace `<create/drop index sql>` with the appropriate SQL statement for the operation.

*   Update `MetadataSource.java`:
    *   Correct the `isOnlineIndexSupported` method to return `true` for:
        *   'Developer Edition (64-bit)'
        *   'Azure SQL Edge Developer (64-bit)'
        *   'Azure SQL Edge Premium (64-bit)'
    *   Ensure it returns `false` for 'Standard Edition (64-bit)'.
    *   Maintain `true` for 'Enterprise Edition (64-bit)'.

*   Ensure that the TRY/CATCH mechanism is used for SQL Server online index operations, bypassing edition detection results for these operations.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.