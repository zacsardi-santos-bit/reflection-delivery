Implement support for dataset aliases in Airflow's scheduling system by persisting the alias-to-dataset relationship in the database. Ensure that DAGs scheduled with dataset aliases are correctly triggered and that timetable descriptions accurately reflect the resolution status of aliases.

*   Implement the `_DatasetAliasCondition` class in `airflow/datasets/__init__.py`:
    *   Constructor must accept a single string argument `name`.
    *   Include an `objects` attribute that returns a list of `Dataset` objects linked to the alias; return an empty list if unresolved.
    *   Implement `as_expression()` to return a dictionary `{'alias': name}`.
    *   Implement `evaluate(statuses)` to return `True` if the alias resolves to datasets and all have `True` status; return `False` if no datasets are linked.

*   Implement the `DatasetAliasModel` class in `airflow/models/dataset.py`:
    *   Constructor must accept a `name` keyword argument.
    *   Include `name` (str) and `datasets` (list of `DatasetModel`) attributes.
    *   Ensure `datasets` supports appending `DatasetModel` instances.

*   Update `DatasetModel` in `airflow/models/dataset.py`:
    *   Add an `aliases` relationship attribute that returns a list of `DatasetAliasModel` objects linked to the dataset.
    *   Ensure bidirectional population of `DatasetModel.aliases` and `DatasetAliasModel.datasets` when a task produces a dataset event via a `DatasetAlias` outlet.

*   Ensure that when multiple `DatasetAlias` outlets produce events for the same dataset URI:
    *   All aliases appear in `DatasetModel.aliases`.
    *   Each `DatasetAliasModel` includes the dataset in its `datasets` list.

*   Update `DatasetTriggeredTimetable` in `airflow/timetables/simple.py`:
    *   Implement the `summary` property to return "unresolved DatasetAlias" if the alias has no linked datasets, and "Dataset" if resolved.

*   Update `DagModel.dags_needing_dagruns` to:
    *   Identify DAGs scheduled with a `DatasetAlias` as needing a run when a `DatasetDagRunQueue` record exists for the underlying dataset linked to the alias.

*   Ensure a `DatasetAlias` used in a DAG's schedule condition serializes as `{'alias': name}` in the DAG's dataset expression, nested inside the appropriate 'any' or 'all' container.

*   Modify the `clear_db_datasets` utility function to:
    *   Delete all `DatasetAliasModel` records from the database when clearing dataset-related tables.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.