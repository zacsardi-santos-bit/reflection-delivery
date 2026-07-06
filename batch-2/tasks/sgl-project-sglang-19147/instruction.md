I'm working on the benchmark datasets module and I'd like to refactor it so each dataset type has its own submodule instead of everything being dumped into a single file.

*   The sample_random_requests function must be importable from sglang.benchmark.datasets.random (not only from the top-level sglang.benchmark.datasets package).

*   The sample_sharegpt_requests, sample_custom_requests, sample_openai_requests, sample_generated_shared_prefix_requests, sample_image_requests, sample_mmmu_requests, and get_mooncake_request_over_time functions must each be importable from their respective submodules: sharegpt, custom, openai_dataset, generated_shared_prefix, image, mmmu, and mooncake under sglang.benchmark.datasets.

*   A DATASET_MAPPING dict must be exported from sglang.benchmark.datasets and must contain at minimum these keys: 'sharegpt', 'custom', 'openai', 'random', 'random-ids', 'generated-shared-prefix', 'mmmu', 'image', 'mooncake'.

*   The get_dataset function in sglang.benchmark.datasets must accept (args, tokenizer, model_id) and dispatch to the correct dataset loader based on args.dataset_name using DATASET_MAPPING.

*   When get_dataset is called with an unrecognized dataset name, it must raise a ValueError.

*   When get_dataset is called with dataset_name='random-ids' and args.tokenize_prompt=True, it must return a list of DatasetRow objects whose prompt attribute is a list (token IDs, not a string).

*   When get_dataset is called with dataset_name='mooncake', it must return a list of dicts (not DatasetRow objects), limited to args.num_prompts entries.

*   When get_dataset is called with dataset_name='sharegpt', it must return a list of DatasetRow objects of length args.num_prompts.

*   The DatasetRow class must be importable from sglang.benchmark.datasets.common and must have prompt, image_data, and extra_request_body attributes.

*   sample_openai_requests must parse optional fields (such as temperature and tools) from JSONL rows and store them in DatasetRow.extra_request_body.

*   sample_generated_shared_prefix_requests must return a list of DatasetRow objects with length equal to num_groups * prompts_per_group.

*   sample_image_requests must return DatasetRow objects with a truthy image_data attribute for each request.

*   get_mooncake_request_over_time must be an async generator that accepts input_requests, tokenizer, slowdown_factor, and num_rounds, and yields DatasetRow objects.


*   Interface details: Type: Function
Name: sample_sharegpt_requests
Location: python/sglang/benchmark/datasets/sharegpt.py
Signature: sample_sharegpt_requests(dataset_path: str, num_requests: int, tokenizer, ...) -> List[DatasetRow]
Description: Loads and samples requests from a ShareGPT-formatted JSON file. Returns a list of DatasetRow objects of length num_requests.

Type: Function
Name: sample_random_requests
Location: python/sglang/benchmark/datasets/random.py
Signature: sample_random_requests(input_len: int, output_len: int, num_prompts: int, range_ratio: float, tokenizer, dataset_path: str, random_sample: bool, return_text: bool) -> List[DatasetRow]
Description: Generates random benchmark requests. When return_text=True, DatasetRow.prompt is a string. When return_text=False, DatasetRow.prompt is a list of token IDs.

Type: Function
Name: sample_custom_requests
Location: python/sglang/benchmark/datasets/custom.py
Signature: sample_custom_requests(dataset_path: str, num_requests: int, tokenizer, ...) -> List[DatasetRow]
Description: Loads and samples requests from a custom JSONL dataset. Returns a list of DatasetRow objects of length num_requests.

Type: Function
Name: sample_openai_requests
Location: python/sglang/benchmark/datasets/openai_dataset.py
Signature: sample_openai_requests(dataset_path: str, num_requests: int, tokenizer, ...) -> List[DatasetRow]
Description: Loads requests from an OpenAI-format JSONL file. For each row, any extra fields (e.g., temperature, tools) are stored in DatasetRow.extra_request_body. Returns a list of DatasetRow objects.

Type: Function
Name: sample_generated_shared_prefix_requests
Location: python/sglang/benchmark/datasets/generated_shared_prefix.py
Signature: sample_generated_shared_prefix_requests(num_groups: int, prompts_per_group: int, system_prompt_len: int, question_len: int, output_len: int, range_ratio: float, tokenizer, args) -> List[DatasetRow]
Description: Generates synthetic requests with shared prefixes per group. Returns a list of DatasetRow objects of length num_groups * prompts_per_group.

Type: Function
Name: sample_image_requests
Location: python/sglang/benchmark/datasets/image.py
Signature: sample_image_requests(num_requests: int, image_count: int, input_len: int, output_len: int, range_ratio: float, processor, image_content: str, image_format: str, image_resolution: str, backend: str, random_image_count: bool) -> List[DatasetRow]
Description: Generates multimodal benchmark requests with image data. Returns a list of DatasetRow objects, each with a truthy image_data attribute.

Type: Function
Name: sample_mmmu_requests
Location: python/sglang/benchmark/datasets/mmmu.py
Signature: sample_mmmu_requests(num_requests: int, processor, backend: str, fixed_output_len: int, random_sample: bool) -> List[DatasetRow]
Description: Samples requests from the MMMU dataset. Returns a list of DatasetRow objects of length num_requests.

Type: Function
Name: get_mooncake_request_over_time
Location: python/sglang/benchmark/datasets/mooncake.py
Signature: get_mooncake_request_over_time(input_requests: list, tokenizer, slowdown_factor: float, num_rounds: int) -> AsyncGenerator[DatasetRow, None]
Description: Async generator that yields DatasetRow objects from Mooncake trace records, simulating request timing.

Type: Variable
Name: DATASET_MAPPING
Location: python/sglang/benchmark/datasets/__init__.py
Signature: DATASET_MAPPING: Dict[str, Any]
Description: A dictionary mapping dataset name strings to their corresponding dataset loaders. Must contain at minimum the following keys: "sharegpt", "custom", "openai", "random", "random-ids", "generated-shared-prefix", "mmmu", "image", "mooncake".

Type: Function
Name: get_dataset
Location: python/sglang/benchmark/datasets/__init__.py
Signature: get_dataset(args, tokenizer, model_id: str = None) -> List
Description: Dispatches to the appropriate dataset loader based on args.dataset_name using DATASET_MAPPING. For "random-ids" with tokenize_prompt=True, returns DatasetRow objects whose prompt attribute is a list. For "mooncake", returns a list of dicts. Raises ValueError for unrecognized dataset names.

Type: Class
Name: DatasetRow
Location: python/sglang/benchmark/datasets/common.py
Description: Data class representing a single benchmark request row. Has attributes: prompt (str or list), image_data (image content or None), and extra_request_body (dict of optional request fields like temperature or tools).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.