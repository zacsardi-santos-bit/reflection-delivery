I've been fine-tuning diffusion models using adapter techniques and I want to track and serve these adapter weights with MLflow, just like I do with my other models.

*   The `mlflow.diffusers` module must expose a `FLAVOR_NAME` constant equal to the string `"diffusers"`.

*   The `mlflow.diffusers` module must expose a `DiffusersAdapterModel` class with attributes `base_model` (str), `adapter_type` (str), `adapter_path` (str), `base_model_revision` (Optional[str]), and `weight_name` (Optional[str]).

*   `DiffusersAdapterModel` must have a `load_pipeline(base_model=None)` method that calls `diffusers.DiffusionPipeline.from_pretrained` with the stored base model, or with the override if one is provided; on OSError it must raise `MlflowException` with a message matching `"Failed to load base model"`.

*   `mlflow.diffusers.save_model(adapter_path, path, base_model, adapter_type="lora", signature=None, metadata=None)` must write an MLmodel file whose `flavors` dict contains both the `"diffusers"` key (i.e. `FLAVOR_NAME`) and a `"python_function"` key.

*   The diffusers flavor config in the MLmodel file must include: `base_model` (the base model identifier), `adapter_type` (default `"lora"`), `adapter_weights` (the string `"adapter_weights"`), and optionally `base_model_revision` and `weight_name`.

*   `save_model` must write `conda.yaml`, `requirements.txt`, and `python_env.yaml` environment files to the model path.

*   `save_model` must add a default model signature whose `inputs` field contains a column named `"prompt"` with type `"string"` when no custom signature is provided; a custom `signature` argument overrides this default.

*   If `metadata` is provided to `save_model`, it must be stored in the MLmodel file under the `"metadata"` key.

*   `save_model` must resolve and store the base model revision (a HuggingFace commit SHA) in the flavor config as `base_model_revision`; this value must survive a `load_model` roundtrip and be accessible as `loaded_model.base_model_revision`.

*   When `adapter_path` points to a single `.safetensors` file, `save_model` must copy it into the `adapter_weights/` subdirectory renamed to `pytorch_lora_weights.safetensors`.

*   When `adapter_path` is a directory containing a single `.safetensors` file, `save_model` must normalize it to `adapter_weights/pytorch_lora_weights.safetensors` (regardless of the original filename).

*   When `adapter_path` is a directory containing multiple files (e.g., both a weight file and a config JSON), `save_model` must copy all files into `adapter_weights/` preserving their names.

*   When a multi-file adapter directory contains non-standard weight filenames (not `pytorch_lora_weights.safetensors` and not `adapter_model.safetensors`), `save_model` must record the first alphabetical weight filename in the flavor config as `weight_name`; the loaded `DiffusersAdapterModel.weight_name` must equal this value.

*   When a PEFT adapter directory contains `adapter_model.safetensors` (and an `adapter_config.json`), `save_model` must record `weight_name` as `"adapter_model.safetensors"` in the flavor config.

*   `save_model` must ignore hidden files (e.g. `.DS_Store`) when scanning adapter directories for weight files.

*   `save_model` must raise `MlflowException` matching `"Unsupported adapter type"` when `adapter_type` is a string but not a supported value (e.g. `"invalid"`).

*   `save_model` must raise `MlflowException` matching `"does not exist"` when `adapter_path` does not exist on disk.

*   `save_model` must raise `MlflowException` matching `"non-empty"` when `base_model` is an empty string or a whitespace-only string, and matching `"must be a"` when `base_model` is `None` or a non-string type.

*   `save_model` must raise `MlflowException` matching `"adapter_type must be a string"` when `adapter_type` is `None`, an integer, or a list.

*   `save_model` must raise `MlflowException` matching `".safetensors"` when `adapter_path` is a non-`.safetensors` file.

*   `save_model` must raise `MlflowException` matching `"not a valid safetensors"` when a `.safetensors` file has invalid content; this validation applies to every `.safetensors` file in a multi-file directory.

*   `save_model` must raise `MlflowException` matching `"no .safetensors"` when `adapter_path` is a directory that contains no `.safetensors` files.

*   `mlflow.diffusers.load_model(model_uri) -> DiffusersAdapterModel` must return a `DiffusersAdapterModel` instance with the correct `base_model`, `adapter_type`, `adapter_path` (which must exist on disk), and `weight_name` attributes; it must work with both local paths and MLflow tracking URIs (`runs:/...`).

*   `mlflow.diffusers.log_model(adapter_path, base_model, name)` must log the adapter to the active run and return a non-None `ModelInfo`; the logged artifacts must include both an `adapter_weights` item and the MLmodel file.

*   When a diffusers model is loaded via `mlflow.pyfunc.load_model`, the underlying `_model_impl` must be an instance of `mlflow.diffusers.wrapper._DiffusersAdapterWrapper`, and the wrapper's `_flavor_conf` dict must contain the `base_model` key.

*   `mlflow.diffusers.wrapper._DiffusersAdapterWrapper.__init__(adapter_path, flavor_conf, model_config=None)` must store `flavor_conf` as `_flavor_conf`; when `model_config` contains a `"base_model"` key, `_load_pipeline()` must use that override instead of `_flavor_conf["base_model"]`.

*   `_DiffusersAdapterWrapper.get_raw_model()` must return a non-None callable (the pipeline).

*   `_DiffusersAdapterWrapper.predict(data, params=None)` must accept: a plain string (returns a list of 1 PNG bytes item), a list of strings (returns one bytes item per string), a dict with a `"prompt"` key (string or list of strings), or a `pandas.DataFrame` with a `"prompt"` column or a single unnamed column; it must return a `list` of `bytes` where each item begins with the PNG header `b"\x89PNG"`.

*   When `params` are passed to `predict`, they must be forwarded as keyword arguments to the underlying pipeline call alongside the `prompt` list.

*   `predict` must raise `MlflowException` matching `"prompt"` when given a DataFrame without a `"prompt"` column (and with more than one column), or a dict without a `"prompt"` key.

*   `predict` must raise `MlflowException` matching `"No prompts"` when given an empty list.

*   `predict` must raise `MlflowException` matching `"must be a string or list of strings"` when the `"prompt"` value in a dict is neither a string nor a list.

*   `predict` must raise `MlflowException` matching `"Unsupported input type"` for input types other than string, list, dict, or DataFrame.

*   `predict` must raise `MlflowException` matching `"Pipeline returned no images"` when the pipeline output has `images` equal to `None` or an empty list.

*   `predict` must raise `MlflowException` matching `"must be strings, not None"` when any prompt value in a list or DataFrame is `None`.

*   `mlflow.diffusers._resolve_base_model_revision(model_id_or_path) -> Optional[str]` must return `None` for absolute paths (starting with `/`) and for relative paths (starting with `./` or `../`); for a HuggingFace-style repo ID it must call `mlflow.utils.huggingface_utils.get_latest_commit_for_repo` and return the SHA; if that call raises any exception it must return `None`; it must still attempt the HF lookup even if a directory with the same name exists in the working directory.

*   `mlflow.diffusers._detect_device(device=None) -> str` must return the explicit device string when one is passed; when called without arguments it must read from the `MLFLOW_DEFAULT_PREDICTION_DEVICE` environment variable.

*   `mlflow.diffusers.get_default_pip_requirements()` must return a list of requirement strings (each in `name==version` format) that includes at minimum: `diffusers`, `transformers`, `torch`, `peft`, and `safetensors`; it must include `accelerate` only if that package is already installed.

*   `mlflow.diffusers.get_default_conda_env()` must return a dict that contains a `"dependencies"` key.

*   The function `is_valid_hf_repo_id` must be importable from `mlflow.utils.huggingface_utils` (it may have been previously located only in `mlflow.transformers.hub_utils`).


*   Interface details: Type: Constant
Name: FLAVOR_NAME
Location: mlflow/diffusers/__init__.py
Description: String constant identifying the diffusers flavor. Value is "diffusers".

Type: Function
Name: save_model
Location: mlflow/diffusers/__init__.py
Signature: save_model(adapter_path: str, path: str, base_model: str, adapter_type: str = "lora", signature=None, metadata=None) -> None
Description: Saves a diffusion model adapter to the given path. Writes an MLmodel file with "diffusers" and "python_function" flavors, environment files (conda.yaml, requirements.txt, python_env.yaml), and copies adapter weights into an "adapter_weights" subdirectory. Validates inputs and raises MlflowException for invalid arguments.

Type: Function
Name: load_model
Location: mlflow/diffusers/__init__.py
Signature: load_model(model_uri: str) -> DiffusersAdapterModel
Description: Loads a saved diffusers adapter model from a local path or MLflow tracking URI (e.g., "runs:/<run_id>/<name>"). Returns a DiffusersAdapterModel instance.

Type: Function
Name: log_model
Location: mlflow/diffusers/__init__.py
Signature: log_model(adapter_path: str, base_model: str, name: str, **kwargs) -> ModelInfo
Description: Logs a diffusion model adapter to the active MLflow run. Returns a ModelInfo object. Logged artifacts include the "adapter_weights" directory and the MLmodel file.

Type: Function
Name: get_default_pip_requirements
Location: mlflow/diffusers/__init__.py
Signature: get_default_pip_requirements() -> List[str]
Description: Returns a list of pip requirement strings (name==version format) covering core dependencies: diffusers, transformers, torch, peft, safetensors. Includes "accelerate" only if it is currently installed.

Type: Function
Name: get_default_conda_env
Location: mlflow/diffusers/__init__.py
Signature: get_default_conda_env() -> dict
Description: Returns a conda environment specification dict containing a "dependencies" key.

Type: Function
Name: _resolve_base_model_revision
Location: mlflow/diffusers/__init__.py
Signature: _resolve_base_model_revision(model_id_or_path: str) -> Optional[str]
Description: Resolves the base model to a HuggingFace commit SHA. Returns None for absolute paths (starting with "/") or relative paths (starting with "./" or "../"). For a HuggingFace repo ID (e.g., "org/model-name"), calls mlflow.utils.huggingface_utils.get_latest_commit_for_repo and returns the SHA; returns None if that call fails. Attempts the HF hub lookup even when a local directory with the same name exists in the current working directory.

Type: Function
Name: _detect_device
Location: mlflow/diffusers/__init__.py
Signature: _detect_device(device: Optional[str] = None) -> str
Description: Returns the device to use for inference. If device is explicitly provided, returns it unchanged. Otherwise reads from the MLFLOW_DEFAULT_PREDICTION_DEVICE environment variable.

Type: Class
Name: DiffusersAdapterModel
Location: mlflow/diffusers/__init__.py
Description: Represents a loaded diffusion model adapter. Exposes attributes: base_model (str), adapter_type (str), adapter_path (str), base_model_revision (Optional[str]), weight_name (Optional[str]).
Signature: load_pipeline(base_model: Optional[str] = None) -> pipeline
  - Calls diffusers.DiffusionPipeline.from_pretrained with the stored base model, or the override if provided.
  - Raises MlflowException matching "Failed to load base model" if an OSError occurs.

Type: Class
Name: _DiffusersAdapterWrapper
Location: mlflow/diffusers/wrapper.py
Description: PyFunc wrapper for diffusion adapter models. Implements the MLflow pyfunc interface.
Signature:
  __init__(self, adapter_path: str, flavor_conf: dict, model_config: Optional[dict] = None) -> None
    - Stores flavor_conf as _flavor_conf attribute.
    - When model_config contains a "base_model" key, _load_pipeline() must use that value instead of _flavor_conf["base_model"].
  get_raw_model(self) -> callable
    - Returns the pipeline (not None, must be callable).
  predict(self, data, params: Optional[dict] = None) -> List[bytes]
    - Accepts: str, List[str], dict with "prompt" key, pandas.DataFrame with "prompt" column or single-column DataFrame.
    - Returns a list of bytes where each item is a PNG image (starts with b"\x89PNG").
    - When params are provided, passes them as keyword arguments to the pipeline call alongside the prompt list.
    - Raises MlflowException matching "prompt" when DataFrame has multiple columns but no "prompt" column.
    - Raises MlflowException matching "prompt" when dict lacks a "prompt" key.
    - Raises MlflowException matching "No prompts" when input is an empty list.
    - Raises MlflowException matching "must be a string or list of strings" when dict "prompt" value is not a str or list.
    - Raises MlflowException matching "Unsupported input type" for unsupported input types.
    - Raises MlflowException matching "Pipeline returned no images" when pipeline output has images equal to None or an empty list.
    - Raises MlflowException matching "must be strings, not None" when any prompt value is None.
  _load_pipeline(self) -> pipeline
    - Internal method that instantiates the DiffusionPipeline using diffusers.DiffusionPipeline.from_pretrained.

Type: Function
Name: is_valid_hf_repo_id
Location: mlflow/utils/huggingface_utils.py
Signature: is_valid_hf_repo_id(repo_id: str) -> bool
Description: Validates whether a string is a valid HuggingFace repository identifier. Previously located in mlflow/transformers/hub_utils.py; must now be importable from mlflow/utils/huggingface_utils.py.

Type: Function
Name: get_latest_commit_for_repo
Location: mlflow/utils/huggingface_utils.py
Signature: get_latest_commit_for_repo(repo_id: str) -> str
Description: Returns the latest commit SHA for the given HuggingFace repository. Used by _resolve_base_model_revision.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.