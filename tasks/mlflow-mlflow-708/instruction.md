Implement support for specifying a conda environment when saving or logging H2O and Keras models in MLflow. Ensure backward compatibility for loading models saved with older versions that lack a data path key in their configuration.

*   H2O Model Integration:
    *   Define a `FLAVOR_NAME` constant in `mlflow/h2o.py` to identify the H2O model flavor in MLmodel configuration files.
    *   Define a `DEFAULT_CONDA_ENV` constant in `mlflow/h2o.py` as a dictionary representing the default conda environment for H2O models.
    *   Update `save_model` function in `mlflow/h2o.py`:
        *   Accept an optional `conda_env` parameter.
        *   Copy the provided conda environment file into the model directory, ensuring the content remains unchanged.
        *   Record the conda environment path in the pyfunc flavor configuration under the `pyfunc.ENV` key.
        *   Use `DEFAULT_CONDA_ENV` if `conda_env` is `None`.
    *   Update `log_model` function in `mlflow/h2o.py` with similar conda environment handling as `save_model`.
    *   Update `load_model` function in `mlflow/h2o.py`:
        *   Ensure backward compatibility by loading models without a 'data' key in the configuration, using a default data path if necessary.

*   Keras Model Integration:
    *   Define a `FLAVOR_NAME` constant in `mlflow/keras.py` to identify the Keras model flavor in MLmodel configuration files.
    *   Define a `DEFAULT_CONDA_ENV` constant in `mlflow/keras.py` as a dictionary representing the default conda environment for Keras models.
    *   Update `save_model` function in `mlflow/keras.py`:
        *   Accept an optional `conda_env` parameter.
        *   Copy the provided conda environment file into the model directory, ensuring the content remains unchanged.
        *   Record the conda environment path in the pyfunc flavor configuration under the `pyfunc.ENV` key.
        *   Use `DEFAULT_CONDA_ENV` if `conda_env` is `None`.
    *   Update `log_model` function in `mlflow/keras.py` with similar conda environment handling as `save_model`.
    *   Update `load_model` function in `mlflow/keras.py`:
        *   Ensure backward compatibility by loading models without a 'data' key in the configuration, using a default data path if necessary.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.