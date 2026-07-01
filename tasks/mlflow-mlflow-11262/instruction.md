Implement a utility function to detect deployment endpoint URIs in MLflow and enhance input validation for model evaluation. Ensure the system can differentiate between deployment endpoints and other URI types, and provide clear error messages for invalid input data.

*   Implement the function `_is_model_deployment_endpoint_uri` in `mlflow/models/evaluation/base.py`:
    *   Accept a single argument `model`.
    *   Return `True` if `model` is a string starting with the `endpoints:/` URI scheme.
    *   Return `False` for all other strings or when `model` is `None`.
    *   Ensure it returns `True` for inputs like `'endpoints:/test'` and `'endpoints:///my-chat'`.
    *   Ensure it returns `False` for inputs like `'models:/test'` and `None`.

*   Enhance `mlflow.evaluate()` to validate input data when using deployment endpoint URIs:
    *   Raise an `MlflowException` with the message `'The number of input columns must be 1'` if:
        *   The input data is a DataFrame with more than one non-target input column.
        *   The input data is a DataFrame with no valid input column.
    *   Raise an `MlflowException` with the message `'Invalid input column type'` if:
        *   The input column contains values that are neither strings nor dictionaries.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.