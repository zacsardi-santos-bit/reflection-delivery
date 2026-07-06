I'm working with a document extraction library that uses OpenAI as one of its inference providers.

*   The BatchConfig class must accept 'enabled', 'threshold', 'completion_window' (default '24h'), 'poll_interval', 'timeout', 'max_requests_per_job', 'metadata' (default None), and 'on_job_create' (default None) parameters. Its default timeout must be greater than 86400 seconds.

*   BatchConfig must validate completion_window on construction: only '24h' is supported. Any other value (e.g., '1h') must raise ValueError with 'completion_window' in the message.

*   BatchConfig.from_dict must return the input unchanged when passed a BatchConfig instance, return BatchConfig(enabled=True) for True and BatchConfig(enabled=False) for False, and raise TypeError with 'batch must be a mapping' for any other type (such as a list).

*   infer_batch must return a list of response strings in the same order as the input prompts, using custom IDs in the format 'idx-{n:06d}' to track and reorder results.

*   infer_batch must raise InferenceConfigError with 'batch_size must be > 0' when batch_size is 0 or negative.

*   When batch_size is provided, infer_batch must split prompts into sub-batches of at most batch_size items, submitting each as a separate batch job and merging all results in original order.

*   When a batch job creation call fails, infer_batch must delete the previously uploaded input file and raise InferenceRuntimeError with 'job create failed' in the message.

*   When a batch job reaches a terminal status of 'failed', 'expired', or 'cancelled', infer_batch must raise InferenceRuntimeError with 'status={status}' in the message.

*   When a failed batch job includes an errors field with error data, infer_batch must raise InferenceRuntimeError whose message contains the text of those errors.

*   When a completed batch job has neither output_file_id nor error_file_id, infer_batch must raise InferenceRuntimeError with 'no output_file_id or error_file_id' in the message.

*   When an output item has a non-null error field, infer_batch must raise InferenceRuntimeError with 'per-item errors' in the message.

*   When an output item has a non-2xx HTTP status_code, infer_batch must raise InferenceRuntimeError with 'status_code={code}' in the message.

*   When an output item contains a refusal instead of content, infer_batch must raise InferenceRuntimeError whose message includes the refusal text.

*   When an expected custom_id is absent from the batch output, infer_batch must raise InferenceRuntimeError with 'custom_id={id}' in the message.

*   When the output contains an unrecognized custom_id, infer_batch must log a WARNING containing 'unexpected custom_id'.

*   When downloading output files returns a 403 error, infer_batch must retry the download. After 3 failed attempts (2 intermediate sleeps of poll_interval), it must raise InferenceRuntimeError with 'Files Read permission' in the message.

*   When a completed job has both output_file_id and error_file_id, infer_batch must read both files; errors from the error file must be raised as InferenceRuntimeError containing the error message.

*   infer_batch must poll batch job status using time.sleep(poll_interval) between checks, and must enforce a timeout using time.time. On timeout, it must cancel the batch job and raise InferenceRuntimeError with 'timed out' in the message.

*   infer_batch must pass cfg.metadata to the batch job creation call, must call cfg.on_job_create with each created job object, and must send the completion_window (default '24h') to the batch create call.

*   OpenAILanguageModel must accept a 'batch' constructor parameter (dict, bool, or BatchConfig) and a 'seed' parameter. The batch configuration must not be forwarded to the underlying chat completions API.

*   OpenAILanguageModel.infer_batch(prompts, batch_size) must call infer_batch from openai_batch, passing the client instance, model_id, a request_builder that includes response_format and seed in each request dict, and the batch_size. It must return a list of lists, where each inner item has an .output attribute.

*   OpenAILanguageModel.infer must route to batch mode when batch is enabled and the number of prompts meets or exceeds the threshold. When batch is enabled but the prompt count is below the threshold, it must log an INFO message containing 'below the threshold' and fall back to real-time inference. InferenceConfigError from infer_batch must propagate to the caller.


*   Interface details: Type: Class
Name: BatchConfig
Location: langextract/providers/openai_batch.py
Description: Configuration dataclass for OpenAI Batch API processing. Controls whether batch mode is active, the prompt threshold, polling interval, timeout, job-splitting size, metadata, and a callback fired on each job creation. The default timeout must be greater than 86400 seconds (more than 24 hours). Only '24h' is supported for completion_window; any other value raises ValueError with 'completion_window' in the message.
Signature: BatchConfig(enabled: bool, threshold: int = ..., completion_window: str = '24h', poll_interval: int | float = ..., timeout: int | float = ..., max_requests_per_job: int = ..., metadata: dict | None = None, on_job_create: callable | None = None)

Type: Class Method
Name: BatchConfig.from_dict
Location: langextract/providers/openai_batch.py
Description: Coerces its argument to a BatchConfig. If the argument is already a BatchConfig, returns it unchanged. If True or False, returns BatchConfig(enabled=True) or BatchConfig(enabled=False). Any other type (e.g., a list) raises TypeError with the message 'batch must be a mapping'.
Signature: from_dict(cls, obj: Any) -> BatchConfig

Type: Function
Name: infer_batch
Location: langextract/providers/openai_batch.py
Description: Submits prompts to the OpenAI Batch API, polls for completion, and returns responses as a list of strings in the same order as the input prompts. Custom IDs are assigned using the format 'idx-{n:06d}'. When batch_size is provided, prompts are split into sub-batches of that size, each submitted as a separate batch job. Uses time.sleep for polling and time.time for timeout enforcement.

Error conditions (all raise from langextract.core.exceptions):
- batch_size=0 or negative: raises InferenceConfigError with 'batch_size must be > 0'
- Job creation failure: raises InferenceRuntimeError with 'job create failed'; the previously uploaded input file is deleted before raising
- Terminal job status ('failed', 'expired', 'cancelled'): raises InferenceRuntimeError with 'status={status}' (e.g., 'status=expired')
- Failed job with errors data: raises InferenceRuntimeError whose message contains the error message from the errors field
- Completed job with neither output_file_id nor error_file_id: raises InferenceRuntimeError with 'no output_file_id or error_file_id'
- Output item with an error field: raises InferenceRuntimeError with 'per-item errors'
- Output item with non-2xx status_code: raises InferenceRuntimeError with 'status_code={code}' (e.g., 'status_code=429')
- Output item with a refusal instead of content: raises InferenceRuntimeError containing the refusal text
- Expected custom_id absent from output: raises InferenceRuntimeError with 'custom_id={id}'
- Unexpected custom_id in output: logs a WARNING containing 'unexpected custom_id'
- Repeated 403 on output file download (3 attempts, 2 sleeps): raises InferenceRuntimeError with 'Files Read permission'
- Timeout exceeded: raises InferenceRuntimeError with 'timed out'; the running batch job is cancelled before raising

Additional behavior:
- cfg.metadata is forwarded to the batch job creation call
- cfg.on_job_create is called with the created job object after each job is submitted
- Default completion_window '24h' is passed to the batch create call
- When a completed job has both output_file_id and error_file_id, both files are read and errors from the error file are raised as InferenceRuntimeError

Signature: infer_batch(client: Any, model_id: str, prompts: list[str], cfg: BatchConfig, request_builder: Callable[[str], dict], batch_size: int | None = None) -> list[str]

Type: Class (modification to existing)
Name: OpenAILanguageModel
Location: langextract/providers/openai.py
Description: Existing language model class extended to accept batch configuration and support batch inference. The constructor now accepts a 'batch' parameter. The batch config must NOT be forwarded as a kwarg to the underlying chat completions API. A new infer_batch method is added. The existing infer method is updated to route through batch mode when enabled and the prompt count meets the threshold, logging an INFO message containing 'below the threshold' when enabled but the count is below the threshold.
Signature:
  __init__(self, ..., batch: dict | bool | BatchConfig | None = None, seed: int | None = None, ...)
  infer_batch(self, prompts: list[str], batch_size: int) -> list[list[Any]]  # each inner item has .output attribute
  infer(self, prompts: list[str], batch_size: int | None = None, ...) -> Iterator[list[Any]]


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.