## Description

The OpenAPI Generator currently has no support for generating PostgreSQL database schema files from OpenAPI specifications. Teams that maintain both an API spec and a PostgreSQL database need to manually translate their model definitions into SQL, which is error-prone and time-consuming.

We need a new generator that produces PostgreSQL-compatible SQL schema definitions from OpenAPI models. The generator should correctly map OpenAPI data types to appropriate PostgreSQL types—for example, choosing the right integer storage type based on declared min/max value ranges, and picking between fixed-length and unlimited text types based on string length constraints. It should also support JSON and JSONB column types, provide configurable identifier naming conventions, handle PostgreSQL reserved word conflicts, and support options like auto-incrementing ID columns and named query parameters.

## Expected Behavior

- Integer fields should map to SMALLINT, INTEGER, or BIGINT based on the declared value range
- String fields should map to VARCHAR or TEXT based on declared minimum and maximum lengths
- Identifiers that conflict with PostgreSQL reserved words or that start with digits should be handled properly (prefixed or escaped)
- Identifiers must be escapable for both unquoted and quoted usage, removing characters that are not valid in each context
- The generator should expose configurable options: default database name, JSON column type (json, jsonb, or off), identifier naming convention (snake_case or original), named parameters mode, and auto-increment for integer ID fields
- Invalid configuration values (e.g., a database name starting with a digit, or an unrecognized naming convention) should be silently rejected, leaving the existing value unchanged

## Why This Matters

Database-first or spec-first teams using PostgreSQL have no automated way to keep their schemas in sync with their OpenAPI models. A dedicated PostgreSQL schema generator closes this gap and enables reliable, repeatable schema generation as part of a code generation workflow.
