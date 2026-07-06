## Description

Airflow supports dataset aliases, which allow tasks to emit events under an alias name that gets resolved to one or more concrete datasets. However, the database relationship between a dataset alias and the datasets it resolves to is not currently tracked or maintained. This means that when a DAG is scheduled to run in response to a dataset alias being updated, the scheduling system cannot correctly determine which underlying datasets to monitor and cannot trigger the DAG run as expected.

Additionally, when a dataset alias is unresolved (no datasets linked), the timetable description provides no indication of this — it looks the same as a fully resolved schedule, which can be confusing for users trying to understand why their DAG isn't running.

## Expected Behavior

- When a task produces output through a dataset alias, the relationship between the alias and the underlying dataset should be persisted in the database, so both the alias and the dataset can be queried to find each other.
- DAGs scheduled via a dataset alias should appear as needing a run when the underlying dataset's queue is updated, following the same rules as any dataset-triggered DAG (max active runs, etc.).
- A dataset alias used in a schedule condition should serialize to an expression that includes the alias name, allowing the expression to be stored and evaluated.
- The timetable description for an unresolved alias should clearly state that the alias has not yet been resolved, while a resolved alias should show the same summary as a regular dataset schedule.

## Why This Matters

Without these changes, dataset-alias-based scheduling is incomplete: aliases can be declared and events can be emitted, but the DAG trigger mechanism doesn't actually fire, and operators can't tell at a glance whether their alias-based schedule is active. This makes dataset aliases unreliable for production use.
