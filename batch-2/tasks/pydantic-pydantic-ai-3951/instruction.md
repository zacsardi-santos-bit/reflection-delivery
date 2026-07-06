I'm using the OpenAI Responses API model and I noticed that the token pre-counting feature doesn't seem to be supported.

*   The count_tokens method on OpenAIResponsesModel must make a POST request to the OpenAI Responses API input token counting endpoint (https://api.openai.com/v1/responses/input_tokens) and return a RequestUsage object with input_tokens populated from the API response.

*   When count_tokens is called with an empty messages list and no previous response ID or conversation ID configured in model settings, it must raise a UserError with a message matching 'Cannot count tokens without any messages or a previous response ID'.

*   When count_tokens is called with a ModelRequestParameters that includes function tools, those tools must be included in the token counting request sent to the API.

*   When model settings are provided (e.g. timeout), those settings must be applied to the token counting request in the same way they are applied to regular inference requests.

*   When an Agent is run with UsageLimits configured with both input_tokens_limit and count_tokens_before_request=True, the OpenAIResponsesModel must perform a token counting call before each inference request and check the result against the limit.

*   If the pre-counted input tokens exceed the configured input_tokens_limit, the agent run must raise UsageLimitExceeded with a message matching 'The next request would exceed the input_tokens_limit of {limit} (input_tokens={actual})'.

*   If the pre-counted input tokens do not exceed the configured input_tokens_limit, the agent run must proceed normally and return the expected output.


*   Interface details: Type: Method
Name: count_tokens
Location: pydantic_ai_slim/pydantic_ai/models/openai.py
Signature: async def count_tokens(self, messages: list[ModelRequest | ModelResponse], model_settings: ModelSettings | None, model_request_parameters: ModelRequestParameters) -> RequestUsage
Description: Counts the number of input tokens that would be consumed by the given messages and model request parameters, using the OpenAI Responses API's input token counting endpoint (/v1/responses/input_tokens). Returns a RequestUsage object with the input_tokens field populated. Raises UserError if messages is empty and no previous response ID is set in model_settings. Must be added to the OpenAIResponsesModel class.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.