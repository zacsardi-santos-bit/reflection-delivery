Implement support for external volume SQL statements in SQLFluff's Snowflake dialect. Ensure that SQL statements for creating, altering, dropping, describing, and restoring external volumes are parsed correctly to prevent linting errors.

*   Add a new `ExternalVolumeReferenceSegment` class in `src/sqlfluff/dialects/dialect_snowflake.py`.
    *   Extend the base object reference segment.
    *   Set `type = "external_volume_reference"`.
    *   Use this segment as the volume name in all external volume statements.
*   Implement `CreateExternalVolumeStatementSegment` in `src/sqlfluff/dialects/dialect_snowflake.py`.
    *   Set `type = "create_external_volume_statement"`.
    *   Parse `CREATE [OR REPLACE] EXTERNAL VOLUME [IF NOT EXISTS] <name> STORAGE_LOCATIONS = (...)`.
    *   Include optional clauses: `ALLOW_WRITES = TRUE|FALSE` and `COMMENT = 'text'`.
    *   Ensure storage locations include NAME, STORAGE_PROVIDER, STORAGE_BASE_URL, and optionally STORAGE_AWS_ROLE_ARN, AZURE_TENANT_ID, and ENCRYPTION settings.
*   Implement `AlterExternalVolumeStatementSegment` in `src/sqlfluff/dialects/dialect_snowflake.py`.
    *   Set `type = "alter_external_volume_statement"`.
    *   Parse `ALTER EXTERNAL VOLUME [IF EXISTS] <name>`.
    *   Support actions: `ADD STORAGE_LOCATION = (...)`, `REMOVE STORAGE_LOCATION 'name'`, `SET ALLOW_WRITES = TRUE|FALSE`, `SET COMMENT = 'text'`.
*   Implement `DropExternalVolumeStatementSegment` in `src/sqlfluff/dialects/dialect_snowflake.py`.
    *   Set `type = "drop_external_volume_statement"`.
    *   Parse `DROP EXTERNAL VOLUME [IF EXISTS] <name>`.
    *   Use `ExternalVolumeReferenceSegment` for the volume name.
*   Extend `DescribeStatementSegment` in `src/sqlfluff/dialects/dialect_snowflake.py`.
    *   Support `DESCRIBE EXTERNAL VOLUME <name>`.
    *   Parse the name as an `external_volume_reference` node.
*   Extend `UndropStatementSegment` in `src/sqlfluff/dialects/dialect_snowflake.py`.
    *   Support `UNDROP EXTERNAL VOLUME <name>`.
    *   Parse the name as an `external_volume_reference` node.
*   Register `CreateExternalVolumeStatementSegment`, `DropExternalVolumeStatementSegment`, and `AlterExternalVolumeStatementSegment` in the Snowflake `StatementSegment`.
*   Add the following keywords to `src/sqlfluff/dialects/dialect_snowflake_keywords.py`:
    *   ALLOW_WRITES, STORAGE_LOCATION, STORAGE_LOCATIONS, STORAGE_BASE_URL, STORAGE_AWS_EXTERNAL_ID, VOLUME, VOLUMES.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.