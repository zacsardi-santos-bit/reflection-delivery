Refactor the time series ensemble model to improve usability and organize hyperparameter tuning utilities. Address the issues with ensemble model instantiation, prediction input validation, and module structure.

*   Update the `TimeSeriesGreedyEnsemble` class:
    *   Modify the `__init__` method to make the `name` parameter optional with a default value, such as "WeightedEnsemble".
    *   Adjust the `predict` method to validate that the ensemble's learned model names are a subset of the keys in the input data dictionary, allowing extra model keys without raising errors.
    *   Ensure the `predict` method raises a `RuntimeError` if any required base model predictions in the data dictionary are `None`.

*   Implement the `fit_ensemble` method in `AbstractTimeSeriesEnsembleModel`:
    *   Ensure it delegates to the internal `_fit_ensemble` method using keyword arguments: `predictions_per_window`, `data_per_window`, and `time_limit`.
    *   Accept `predictions_per_window` (a dictionary mapping model names to lists of `TimeSeriesDataFrame`) and `data_per_window` (a list of `TimeSeriesDataFrame`), and fit across varying numbers of windows, models, and prediction lengths.
    *   Ensure the resulting `TimeSeriesDataFrame` from `predict` has an index matching the input model predictions and contains no `NaN` values.

*   Reorganize the hyperparameter tuning utilities:
    *   Move or re-export the `skip_hpo` function to a new module located at `timeseries/src/autogluon/timeseries/models/abstract/tunable.py`.
    *   Ensure the `skip_hpo` function is importable from `autogluon.timeseries.models.abstract.tunable` and is used by the `hyperparameter_tune` method when the HPO search space is empty.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.