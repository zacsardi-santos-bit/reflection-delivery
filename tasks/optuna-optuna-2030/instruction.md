Refactor the relational database schema to support multi-objective optimization by storing optimization directions and trial objective values in separate tables. Implement methods for retrieving and managing these records, ensuring proper cascade deletion and schema versioning.

*   Implement `StudyDirectionModel` in `optuna/storages/_rdb/models.py`:
    *   Store one optimization direction per objective index for a study.
    *   Accept `study_id`, `direction` (a `StudyDirection` enum value), and `objective` (an integer index) upon construction.
    *   Implement `find_by_study_and_objective(study, objective, session)` to return a matching `StudyDirectionModel` instance or `None`.
    *   Implement `where_study(study, session)` to return a list of all `StudyDirectionModel` instances for a study.
    *   Ensure cascade deletion of `StudyDirectionModel` records when a `StudyModel` is deleted.

*   Update `StudyModel` in `optuna/storages/_rdb/models.py`:
    *   Modify the constructor to accept a `directions` parameter (list of `StudyDirectionModel` instances).
    *   Maintain a relationship attribute `directions` for associated `StudyDirectionModel` instances.

*   Implement `TrialValueModel` in `optuna/storages/_rdb/models.py`:
    *   Store one objective value per objective index for a trial.
    *   Accept `trial_id`, `objective` (an integer index), and `value` (a numeric value) upon construction.
    *   Implement `find_by_trial_and_objective(trial, objective, session)` to return a matching `TrialValueModel` instance or `None`.
    *   Implement `where_trial_id(trial_id, session)` to return a list of all `TrialValueModel` instances for a trial.

*   Update `TrialModel` in `optuna/storages/_rdb/models.py`:
    *   Expose a `values` relationship attribute for associated `TrialValueModel` instances.
    *   Ensure cascade deletion of `TrialValueModel` records when a `TrialModel` is deleted.

*   Implement `TrialIntermediateValueModel` methods:
    *   Implement `find_by_trial_and_step(trial, step, session)` to return a matching `TrialIntermediateValueModel` instance or `None`.
    *   Implement `where_trial_id(trial_id, session)` to return a list of all `TrialIntermediateValueModel` instances for a trial.
    *   Ensure cascade deletion of `TrialIntermediateValueModel` records when a `TrialModel` is deleted.

*   Update schema versioning in `optuna/storages/_rdb/storage.py`:
    *   Modify `get_all_versions()` to include the new head version `"v2.4.0.a"`, resulting in the list `["v2.4.0.a", "v1.3.0.a", "v1.2.0.a", "v0.9.0.a"]`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.