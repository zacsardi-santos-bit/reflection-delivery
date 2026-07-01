I'm working with SQLFluff to lint Snowflake SQL files, but it fails to parse any statements that manage external volumes. These are statements that create, alter, drop, describe, or restore external cloud storage volumes in Snowflake. Right now, any SQL file containing these statements either causes a parse error or gets flagged as invalid — even though the SQL is perfectly valid Snowflake syntax.

I need the Snowflake dialect to support these statement types:

- Creating an external volume with one or more cloud storage locations (supporting S3, GCS, and Azure providers), including optional encryption settings, role ARNs, tenant identifiers, write-access flags, and comments. The create statement should support both "replace if exists" and "create if not exists" variants.
- Altering an external volume to add a storage location, remove a storage location by name, or set properties like write access and comments. The alter statement should also support an "if exists" guard.
- Dropping an external volume, with or without an existence guard. This should be its own distinct statement type, separate from the general drop statement.
- Describing an external volume should work under the existing describe statement support.
- Restoring (undroping) an external volume should work under the existing undrop statement support.

In all cases, the volume name should be parsed as a dedicated external volume reference node in the parse tree.
