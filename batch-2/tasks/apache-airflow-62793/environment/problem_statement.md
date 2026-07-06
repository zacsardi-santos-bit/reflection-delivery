## Description

We need a new Airflow operator that can compare database schemas across different systems and flag incompatibilities using an LLM. Currently, Airflow has no built-in way to detect schema drift when migrating or synchronizing data between different databases (e.g., PostgreSQL to Snowflake) or between a database and file-based storage (e.g., an S3 Parquet file and a relational table). Teams often encounter silent failures or data corruption because a column type changed or a column was added/dropped in one system but not the other.

## Expected Behavior

- A new operator should accept connections to two or more data sources — which can be any mix of relational databases and file-based cloud storage — and introspect their schemas automatically.
- The operator should use an LLM to analyze the gathered schema information, identify mismatches, and return a structured result indicating whether the schemas are compatible, a list of mismatches, and a summary.
- The operator should support at least two context modes: a basic mode (columns only) and a full mode that also includes primary keys, foreign keys, and indexes.
- Input can be specified either as a list of database connection identifiers plus table names, as a list of data source configurations, or a combination of both — but at least two sources must always be provided.
- A task decorator variant should also be provided, allowing developers to write a Python function that returns the comparison prompt at runtime; if the function returns anything other than a non-empty string, the task should fail with a clear error.

## Why This Matters

Schema drift is one of the most common causes of data pipeline failures, yet detecting it proactively requires writing custom introspection and comparison logic for each combination of source and target systems. By adding LLM-powered schema comparison as a first-class Airflow operator, teams can add schema validation gates to their DAGs without writing system-specific introspection code.
