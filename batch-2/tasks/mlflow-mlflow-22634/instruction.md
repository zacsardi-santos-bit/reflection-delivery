I'm working with MLflow's third-party evaluation scorers (the wrappers for external evaluation libraries) and I've run into two issues.

*   Third-party scorer wrapper classes (for RAGAS, DeepEval, TruLens, and Phoenix) must expose a `kind` property that returns `ScorerKind.THIRD_PARTY`.

*   Each third-party scorer instance must store `_metric_name` (str), `_model` (str or None), and `_metric_kwargs` (dict) as private attributes. TruLens scorers must additionally store `_threshold` (float) as a private attribute.

*   Calling `model_dump()` on a third-party scorer must return a dict with a `third_party_scorer_data` key whose value is a dict containing: `module` (the scorer class's Python module path), `class` (the class name string), `metric_name` (the metric's canonical name), `model` (the LLM model URI string or None for deterministic scorers), and `kwargs` (a dict of extra constructor arguments).

*   When a registered name differs from the class-level metric name, `model_dump()` must record the registered display name in `name` while `third_party_scorer_data.metric_name` remains bound to the class-level metric name. After deserialization via `Scorer.model_validate()`, the restored scorer's `name` must equal the registered display name.

*   `Scorer.model_validate()` must reconstruct the correct third-party scorer subclass from a serialized dict containing `third_party_scorer_data`. The reconstructed instance must be of the correct type, have the correct `name`, `kind`, `_model`, and (for TruLens) `_threshold` restored.

*   When `Scorer.model_validate()` processes a third-party scorer payload, it must raise `MlflowException` with a message matching 'not in the allow-list' if the `module` field is not in the allowed module set. Allowed modules are: `mlflow.genai.scorers.ragas`, `mlflow.genai.scorers.deepeval`, `mlflow.genai.scorers.trulens`, `mlflow.genai.scorers.phoenix` (and their submodules).

*   When `Scorer.model_validate()` processes a third-party scorer payload, it must raise `MlflowException` with a message matching 'missing required fields' if the `class` field is empty or the `metric_name` field is empty.

*   When `Scorer.model_validate()` processes a third-party scorer payload, it must raise `MlflowException` with a message matching 'could not import' if importing the module raises an `ImportError`.

*   When `Scorer.model_validate()` processes a third-party scorer payload, it must raise `MlflowException` with a message matching 'not found in module' if the named class does not exist in the imported module.

*   When `Scorer.model_validate()` processes a third-party scorer payload, it must raise `MlflowException` with a message matching 'does not match class' if the stored `metric_name` disagrees with a `metric_name` ClassVar present on the resolved class.

*   When `Scorer.model_validate()` processes a third-party scorer payload, it must raise `MlflowException` with a message matching 'failed to instantiate' if calling the scorer class constructor raises any exception.

*   Calling `register()` on any third-party scorer when the active tracking URI is a Databricks URI must raise `MlflowException` with a message matching 'Third-party scorer registration'.

*   `SerializedScorer` must accept a `third_party_scorer_data` field (dict or None). Constructing a `SerializedScorer` with both `builtin_scorer_class` and `third_party_scorer_data` populated must raise `ValueError` with a message matching 'cannot have multiple types'.

*   `extract_model_from_serialized_scorer(data)` must return the value of `data['third_party_scorer_data']['model']` when `third_party_scorer_data` is present, including returning `None` for deterministic scorers.

*   `update_model_in_serialized_scorer(data, new_model)` must update `third_party_scorer_data.model` to `new_model` (returning a new dict, not mutating the original) when the scorer has a `third_party_scorer_data` key with a non-None `model`. If the existing `model` is `None` (deterministic scorer), the returned dict must still have `model` as `None` — it must not be set to `new_model`.


*   Interface details: Type: Class
Name: SerializedScorer
Location: mlflow/genai/scorers/base.py
Description: Dataclass representing a serialized scorer. Must include a new optional field `third_party_scorer_data: dict[str, Any] | None = None`. Its `__post_init__` validation must count `third_party_scorer_data` as a scorer type field; if more than one scorer type field is populated simultaneously (e.g., both `builtin_scorer_class` and `third_party_scorer_data`), it must raise `ValueError` with a message matching "cannot have multiple types".
Signature: SerializedScorer(name: str, ..., third_party_scorer_data: dict[str, Any] | None = None)

Type: Class
Name: Scorer
Location: mlflow/genai/scorers/base.py
Description: Base scorer class. `model_dump()` must serialize third-party scorers (when `self.kind == ScorerKind.THIRD_PARTY`) into a dict with key `third_party_scorer_data` containing `{"module": <class module>, "class": <class name>, "metric_name": self._metric_name, "model": self._model, "kwargs": dict(self._metric_kwargs)}`. `model_validate()` must reconstruct third-party scorers from a `SerializedScorer` with `third_party_scorer_data` populated, performing validation and raising `MlflowException` on security or integrity failures. `register()` must raise `MlflowException` matching "Third-party scorer registration" when `self.kind == ScorerKind.THIRD_PARTY` and the active tracking URI is a Databricks URI. CRITICAL IMPLEMENTATION CONSTRAINTS: (a) `importlib` must be imported at module level in base.py as `import importlib`, and module loading inside `model_validate()` must call it as `importlib.import_module(module_path)` — NOT via `from importlib import import_module`. Tests patch `mlflow.genai.scorers.base.importlib.import_module` to simulate import failures. (b) `is_databricks_uri` must be imported at module scope in base.py (not inside a function body). Tests patch `mlflow.genai.scorers.base.is_databricks_uri` to simulate Databricks environments.
Signature: model_dump(self) -> dict
Signature: model_validate(cls, data: Any) -> Scorer  [classmethod]
Signature: register(self, name: str, **kwargs) -> Any

Type: Class
Name: DeepEvalScorer
Location: mlflow/genai/scorers/deepeval/__init__.py
Description: Base class for DeepEval-backed scorers. Must store `_metric_name: str`, `_model: str | None`, and `_metric_kwargs: dict` as private attributes. Must return `ScorerKind.THIRD_PARTY` from the `kind` property. Must NOT override `register()`, `start()`, `update()`, or `stop()` to raise unconditional errors; registration blocking is handled by the base `Scorer.register()`.

Type: Class
Name: ExactMatch
Location: mlflow/genai/scorers/deepeval/__init__.py
Description: Concrete DeepEval scorer. Serializes with `third_party_scorer_data.class == "ExactMatch"`, `metric_name == "ExactMatch"`, `model == None`. After deserialization via `Scorer.model_validate()`, instance is of type `ExactMatch`, `name == "ExactMatch"`, `kind == ScorerKind.THIRD_PARTY`.

Type: Class
Name: AnswerRelevancy
Location: mlflow/genai/scorers/deepeval/__init__.py
Description: Concrete DeepEval scorer. When constructed with a model URI, serializes with `third_party_scorer_data.model == <model_uri>`. After deserialization, `_model == <model_uri>`.

Type: Class
Name: RagasScorer
Location: mlflow/genai/scorers/ragas/__init__.py
Description: Base class for RAGAS-backed scorers. Must store `_metric_name: str`, `_model: str | None`, and `_metric_kwargs: dict` as private attributes. Must return `ScorerKind.THIRD_PARTY` from the `kind` property. Must NOT override `register()`, `start()`, `update()`, or `stop()` to raise unconditional errors.

Type: Class
Name: ExactMatch
Location: mlflow/genai/scorers/ragas/__init__.py (or submodule)
Description: Concrete RAGAS scorer. `model_dump()["third_party_scorer_data"]` must equal `{"module": ExactMatch.__module__, "class": "ExactMatch", "metric_name": "ExactMatch", "model": None, "kwargs": {}}`. After deserialization, instance is `ExactMatch`, `name == "ExactMatch"`, `kind == ScorerKind.THIRD_PARTY`, callable (returns feedback with `value == 1.0` when output matches expected).

Type: Class
Name: Faithfulness
Location: mlflow/genai/scorers/ragas/__init__.py (or submodule)
Description: Concrete RAGAS scorer. When constructed with a model URI, serializes with `third_party_scorer_data.model == <model_uri>`. After deserialization, `_model == <model_uri>`.

Type: Class
Name: PhoenixScorer
Location: mlflow/genai/scorers/phoenix/__init__.py
Description: Base class for Phoenix-backed scorers. Must store `_metric_name: str`, `_metric_kwargs: dict` as private attributes. Must expose a `kind` property returning `ScorerKind.THIRD_PARTY`. Must NOT override `register()` to raise unconditional errors.

Type: Class
Name: Hallucination
Location: mlflow/genai/scorers/phoenix/__init__.py
Description: Concrete Phoenix scorer. `kind` must be `ScorerKind.THIRD_PARTY`. When constructed with model URI, serializes with `third_party_scorer_data.class == "Hallucination"`, `metric_name == "Hallucination"`, `model == <model_uri>`. After deserialization, `kind == ScorerKind.THIRD_PARTY`, `_model == <model_uri>`.

Type: Class
Name: TruLensScorer
Location: mlflow/genai/scorers/trulens/__init__.py
Description: Base class for TruLens-backed scorers. Must store `_metric_name: str` and `_metric_kwargs: dict` as private attributes. Must expose a `kind` property returning `ScorerKind.THIRD_PARTY`. Must NOT override `register()` to raise unconditional errors.

Type: Class
Name: Groundedness
Location: mlflow/genai/scorers/trulens/__init__.py
Description: Concrete TruLens scorer. `kind` must be `ScorerKind.THIRD_PARTY`. When constructed with `model` and `threshold`, serializes with `third_party_scorer_data.model == <model_uri>` and `third_party_scorer_data.kwargs.threshold == <threshold>`. After deserialization, `kind == ScorerKind.THIRD_PARTY`, `_model == <model_uri>`, `_threshold == <threshold>`.

Type: Constant
Name: THIRD_PARTY_SCORER_ALLOWED_MODULES
Location: mlflow/genai/scorers/scorer_utils.py
Description: A `frozenset` of allowed module path prefixes for third-party scorer deserialization. Must contain exactly: `"mlflow.genai.scorers.ragas"`, `"mlflow.genai.scorers.deepeval"`, `"mlflow.genai.scorers.trulens"`, `"mlflow.genai.scorers.phoenix"`. Submodule paths are accepted via prefix matching (e.g., `"mlflow.genai.scorers.ragas.scorers.rag_metrics"` matches).

Type: Constant
Name: THIRD_PARTY_SCORER_REGISTRATION_NOT_SUPPORTED_ON_DATABRICKS_ERROR
Location: mlflow/genai/scorers/scorer_utils.py
Description: Error message string used when a third-party scorer's `register()` is called against a Databricks backend. Must start with "Third-party scorer registration" so that `pytest.raises(MlflowException, match="Third-party scorer registration")` matches.

Type: Function
Name: extract_model_from_serialized_scorer
Location: mlflow/genai/scorers/scorer_utils.py
Description: Extended to support third-party scorers. When the input dict contains `third_party_scorer_data`, must return `data["third_party_scorer_data"].get("model")` — which is the model URI string for LLM-backed scorers and `None` for deterministic scorers.
Signature: extract_model_from_serialized_scorer(serialized_data: dict[str, Any]) -> str | None

Type: Function
Name: update_model_in_serialized_scorer
Location: mlflow/genai/scorers/scorer_utils.py
Description: Extended to support third-party scorers. When the input dict contains `third_party_scorer_data` with a non-None `model`, must return a new dict (without mutating the original) with `third_party_scorer_data.model` updated to `new_model`. When `third_party_scorer_data.model` is `None` (deterministic scorer), the returned dict must preserve `model` as `None` — it must not be set to `new_model`.
Signature: update_model_in_serialized_scorer(serialized_data: dict[str, Any], new_model: str) -> dict[str, Any]


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.