## Description

Airflow currently has no native support for AWS Glue Data Quality. Teams using Glue Data Quality to define rulesets and run evaluations against their data tables cannot integrate these checks into their Airflow DAGs without custom code.

We need to add a hook, operators, a sensor, and a trigger to support the full workflow:
1. Creating or updating a data quality ruleset
2. Starting a ruleset evaluation run against a data source
3. Monitoring the evaluation run until it completes (synchronously, via polling, or in deferrable mode)
4. Surfacing failures when one or more rules fail during evaluation

## Expected Behavior

- A hook that provides low-level access to Glue Data Quality APIs, including checking ruleset existence, validating evaluation run results, and a custom waiter for evaluation run completion
- An operator to create or update a data quality ruleset, with validation that the ruleset string is in the correct format and proper error propagation when the ruleset already exists (on create) or is not found (on update)
- An operator to start a ruleset evaluation run, with validation that all referenced rulesets exist, support for passing additional run options, and support for waiting, polling, or deferring until completion — returning the run ID
- A sensor that can poll or defer until an evaluation run reaches a terminal state, with clear failure messages and soft-fail support
- A trigger for deferrable execution that signals completion with the run ID

## Why This Matters

Data quality is a critical concern in data pipelines. Without native Airflow support, teams cannot cleanly orchestrate Glue Data Quality checks alongside their existing ETL tasks, making it difficult to halt pipelines when data quality standards are not met.
