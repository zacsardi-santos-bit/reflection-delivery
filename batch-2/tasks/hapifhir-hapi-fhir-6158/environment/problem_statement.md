## Description

When performing database schema migrations against SQL Server, the migration framework needs to create and drop indexes without locking tables where possible. Currently, the system queries the SQL Server edition at startup and decides whether online (non-locking) index operations are supported. If the edition doesn't support it, the system falls back to the locking approach. This pre-detection mechanism has two issues:

1. **Wrong edition classification**: SQL Server Developer Edition is incorrectly classified as *not* supporting online index operations, even though it does. This means environments running on Developer Edition unnecessarily take table locks during migrations.
2. **Missing Azure SQL Edge support**: Azure SQL Edge editions (Developer and Premium variants) are not handled and get classified incorrectly.

## Expected Behavior

- For SQL Server, both index creation and index dropping with the online flag enabled should use a TRY/CATCH approach at the database level: first attempt the online (non-locking) operation, and if it fails (e.g., because the edition truly doesn't support it), automatically fall back to the standard locking operation. This removes the dependency on correct edition detection for the common create/drop index path.
- The edition detection logic should be corrected to properly recognize SQL Server Developer Edition and Azure SQL Edge (Developer and Premium) editions as supporting online operations.
- SQL Server Standard Edition should continue to be recognized as not supporting online index operations.

## Why This Matters

The current approach causes Developer Edition environments to take unnecessary table locks during migrations. Azure SQL Edge users get no online migration support at all. The TRY/CATCH approach is more robust: it works correctly regardless of whether the edition detection is accurate, since the database itself decides at runtime whether to run the online or the locking path.
