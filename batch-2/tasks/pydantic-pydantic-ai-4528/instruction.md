I'm running into a bug when using the OpenRouter gateway.

*   When a completion response has null standard fields (id, choices, model, object) but the provider field contains a dict with a valid nested completion structure (including its own choices list, model, id, and object fields), the OpenRouter model must successfully unwrap and process the nested completion, returning the message content from the nested choices as normal response parts.

*   When unwrapping a nested completion from the provider metadata dict, the returned provider_details dict must include a 'downstream_provider' key set to the string value of the nested dict's own provider field; if that nested provider value is null or not a string, 'downstream_provider' must be set to the string 'unknown'.

*   When a completion response has null standard fields but the provider field is a dict that does not contain a valid choices list (i.e., cannot be interpreted as a nested completion), processing must raise UnexpectedModelBehavior.

*   When a completion response has null standard fields and contains an error field that is a dict with an integer 'code' key and a string 'message' key, processing must raise ModelHTTPError with its status_code attribute equal to the integer code, and with the error message string appearing in the exception's string representation.

*   When the error field is present but is not a dict (e.g., a plain string), processing must raise UnexpectedModelBehavior rather than ModelHTTPError.

*   When an error dict contains extra fields beyond 'code' and 'message' (such as a 'metadata' key), those extra fields must be silently ignored; ModelHTTPError must still be raised with the correct status_code and message text.


*   Interface details: Type: Method
Name: _validate_completion
Location: pydantic_ai_slim/pydantic_ai/models/openrouter.py
Signature: _validate_completion(self, response: chat.ChatCompletion) -> _OpenRouterChatCompletion
Description: Override of the parent class method in OpenRouterModel. Must be modified to detect and handle two non-standard response shapes before falling back to the standard validation path: (1) responses where standard fields are null but an error dict with 'code' and 'message' keys is present — these must raise ModelHTTPError; (2) responses where standard fields are null but the provider field is a dict containing a nested completion — these must be unwrapped. If neither shape matches, the original validation error must propagate as UnexpectedModelBehavior.

Type: Class
Name: ModelHTTPError
Location: pydantic_ai_slim/pydantic_ai/exceptions.py (existing class)
Description: Exception raised for HTTP-level model errors. Has a status_code integer attribute. Must be raised when an error response dict (with integer 'code' and string 'message' fields) is detected in a response with null standard fields. The exception's string representation must include the error message text.

Type: Class
Name: UnexpectedModelBehavior
Location: pydantic_ai_slim/pydantic_ai/exceptions.py (existing class)
Description: Exception raised when a model response cannot be parsed or interpreted. Must be raised when a provider dict is present but cannot be unwrapped as a nested completion (no choices), and when the error field is present but is not a dict.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.