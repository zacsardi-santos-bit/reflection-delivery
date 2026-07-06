I'm working on refactoring the time series ensemble models in this codebase and running into a few issues that need to be fixed together.

First, the greedy ensemble model currently requires a name argument when it's created. I'd like to make the name optional so the model can be instantiated with no arguments at all, falling back to a reasonable default name.

Second, the prediction method on the ensemble is too strict about which models are allowed in the input data. Right now, if you pass in predictions for models beyond the ones the ensemble learned weights for, it throws an error. The correct behavior should be to accept any input that contains at least the models the ensemble knows about — extra models in the input should just be ignored.

Third, I need to reorganize some hyperparameter tuning utilities into a new dedicated module. Specifically, the function that handles the case where the hyperparameter search space is empty needs to move (or be re-exported) to a new dedicated submodule within the abstract models package. The existing code that calls this function during hyperparameter tuning needs to be updated to reference the new location.

After these changes, the ensemble should be able to fit correctly using multi-window validation data (where each base model provides predictions for several different time windows), and the resulting predictions should have no missing values and should align in time with the input forecasts. If any base model's predictions are missing entirely, predict should raise a runtime error.
