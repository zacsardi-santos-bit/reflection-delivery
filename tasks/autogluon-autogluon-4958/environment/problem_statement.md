## Description

The time series ensemble model has two usability issues that need to be fixed, and the codebase needs a structural reorganization to separate hyperparameter tuning utilities into a dedicated module.

**Issue 1: Ensemble model requires an explicit name**

The greedy ensemble model currently requires a name argument at instantiation time. This makes it awkward to create ensemble instances in automated or default-usage contexts, where a sensible default name should be sufficient. The model should be constructible without any arguments.

**Issue 2: Prediction rejects valid inputs with extra models**

When calling predict on the greedy ensemble, the current validation checks that the set of models in the input data exactly matches the set of models learned during ensemble fitting. This is too strict: if the caller provides predictions for additional models beyond what the ensemble uses, the call fails with an error. The correct behavior is to accept any input data that includes (at minimum) the models the ensemble learned — extra entries should be silently ignored.

**Issue 3: Module reorganization for hyperparameter tuning utilities**

The hyperparameter tuning utilities, including the helper that handles skipping the tuning process when no search space is defined, must be relocated to a new dedicated module within the abstract models package. Any code that previously relied on finding these utilities in the main abstract model file will need to reference the new location.

## Expected Behavior

- The ensemble model can be instantiated with no arguments, using a default name
- Calling predict with a superset of model names succeeds and uses only the relevant models
- The hyperparameter tuning helper lives in its own dedicated module under the abstract models package
- Fitting and predicting across different numbers of validation windows, base models, and prediction lengths produces valid (NaN-free) forecasts whose time index aligns with the input predictions

## Why This Matters

These changes collectively make the ensemble more flexible and robust in multi-window validation scenarios, and keep related tuning logic cleanly separated from the core model logic.
