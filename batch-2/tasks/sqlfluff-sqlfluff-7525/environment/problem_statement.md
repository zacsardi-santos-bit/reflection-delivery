# Add support for parsing T-SQL RESTORE DATABASE statements

## Description

The T-SQL dialect currently does not support parsing database restore statements. When running the linter against T-SQL scripts that contain restore commands — a very common operation in SQL Server database administration — the parser fails to recognize the syntax and reports parse errors or leaves tokens unrecognized. This makes sqlfluff unusable for many real-world T-SQL codebases that include database maintenance scripts.

## Expected Behavior

- RESTORE DATABASE statements should be parsed cleanly without any errors
- The parser should handle the full range of commonly used restore syntax variants, including:
  - Restoring from disk, tape, or URL (cloud storage) backup sources
  - Recovery options: simple recovery, no-recovery mode, and standby mode
  - File and filegroup specification before the backup source
  - Options like replace existing database, checksum validation, progress reporting
  - Media and transfer tuning options (block size, buffer count, max transfer size)
  - Move file options to redirect data and log files to new paths
  - Using variables for the database name or backup path

## Why This Matters

Database backup and restore scripts are a core part of SQL Server database administration. Without support for this syntax, any T-SQL project that includes these scripts will produce false positives when linted. Adding proper parse support allows these scripts to be linted without noise, bringing T-SQL restore scripts fully into the set of supported syntax.
