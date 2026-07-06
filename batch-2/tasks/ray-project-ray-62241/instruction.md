I'm working on the LLM module in Ray and I need to introduce a validated configuration model for resource placement bundles.

*   BundleConfig must be a Pydantic model located at ray/llm/_internal/common/placement.py. It must have CPU and GPU fields that default to 0.0 (float). When integer values are provided (e.g., CPU=2, GPU=1), they must be coerced to floats in model_dump() output.

*   BundleConfig must support fractional GPU values (e.g., GPU=0.5) and include them correctly in model_dump() output.

*   BundleConfig must accept arbitrary extra keyword arguments as additional named resources (e.g., TPU=4.0). These extra resources must appear in model_dump() output.

*   BundleConfig must raise a ValueError with a message containing 'non-negative' when a negative value is provided for any extra resource (e.g., TPU=-1.0).

*   BundleConfig must raise a ValueError with a message containing 'must be a number' when a non-numeric value is provided for any field (e.g., bad='x').

*   PlacementGroupConfig must be a Pydantic model in the same file. It must accept an optional 'bundles' field (a list of BundleConfig), an optional 'bundle_per_worker' field (a single BundleConfig or a raw dict that is coerced to BundleConfig), and a required 'strategy' field.

*   PlacementGroupConfig's 'strategy' field must only accept valid values (such as 'PACK', 'SPREAD', 'STRICT_PACK', 'STRICT_SPREAD'). An invalid strategy string must raise a Pydantic ValidationError.

*   PlacementGroupConfig must raise a ValueError with a message matching 'either \'bundle_per_worker\'' when neither 'bundles' nor 'bundle_per_worker' is provided.

*   PlacementGroupConfig must raise a ValueError with a message matching 'Cannot specify both' when both 'bundles' and 'bundle_per_worker' are provided simultaneously.

*   PlacementGroupConfig must coerce a raw dictionary provided for 'bundle_per_worker' into a BundleConfig instance, so that attributes like .CPU and .GPU are accessible directly on the resulting object.

*   PlacementGroupConfig.model_dump() must return a dict with 'bundles' (list of resource dicts) or 'bundle_per_worker' (resource dict) and 'strategy' matching the values provided at construction.


*   Interface details: Type: Class
Name: BundleConfig
Location: python/ray/llm/_internal/common/placement.py
Description: A Pydantic model representing a resource bundle for placement. Has CPU (float, default 0.0) and GPU (float, default 0.0) fields. Accepts arbitrary extra named resource fields as floats. Integer inputs are coerced to float. Negative extra resource values raise ValueError with "non-negative" in the message. Non-numeric values raise ValueError with "must be a number" in the message.
Signature: BundleConfig(CPU: float = 0.0, GPU: float = 0.0, **extra_resources: float) -> BundleConfig

Type: Class
Name: PlacementGroupConfig
Location: python/ray/llm/_internal/common/placement.py
Description: A Pydantic model representing a placement group configuration. Accepts an optional list of BundleConfig as 'bundles', an optional single BundleConfig (or raw dict coerced to BundleConfig) as 'bundle_per_worker', and a required 'strategy' string validated against allowed values. Exactly one of 'bundles' or 'bundle_per_worker' must be provided. Raw dicts for bundle fields are automatically coerced into BundleConfig instances.
Signature:
  - PlacementGroupConfig(bundles: Optional[List[BundleConfig]] = None, bundle_per_worker: Optional[BundleConfig] = None, strategy: str) -> PlacementGroupConfig
  - Raises ValueError matching "either 'bundle_per_worker'" when neither bundles nor bundle_per_worker is specified
  - Raises ValueError matching "Cannot specify both" when both bundles and bundle_per_worker are specified
  - Raises pydantic.ValidationError for invalid strategy values
  - bundle_per_worker attribute is a BundleConfig instance with accessible .CPU and .GPU attributes


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.