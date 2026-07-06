Implement a new chat model class, `ChatAnthropicBedrock`, in the `langchain_anthropic` package to support Claude models via AWS Bedrock. Ensure it functions as a drop-in replacement for existing models, with AWS credentials and region handling, and includes necessary utility functions.

*   Export `ChatAnthropicBedrock` from the `langchain_anthropic` package top-level and include it in the `__all__` list.
*   Implement `ChatAnthropicBedrock` with the following constructor parameters:
    *   `model` (required, str)
    *   `region_name` (optional, str)
    *   `aws_access_key_id`, `aws_secret_access_key`, `aws_session_token` (optional, stored as `SecretStr`)
    *   `default_request_timeout` (optional, float)
    *   `max_retries` (int)
    *   `temperature` (float)
    *   `max_tokens` (int)
*   Ensure AWS credentials and region are automatically read from environment variables if not provided explicitly.
    *   Use `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`, `AWS_SESSION_TOKEN` for credentials.
    *   Use `AWS_REGION`, then `AWS_DEFAULT_REGION` for region inference.
*   Implement properties and methods:
    *   `lc_secrets`: Map AWS credentials to environment variable names.
    *   `_client_params`: Return a dictionary with AWS credentials and configuration.
    *   `_client` and `_async_client`: Ensure these attributes are accessible.
    *   `_get_ls_params()`: Return `{"ls_provider": "anthropic-bedrock"}`.
    *   `_get_request_payload(messages)`: Return a dictionary with keys `model`, `temperature`, `max_tokens`, and `messages`.
    *   `profile`: Resolve model names in various formats and include `max_input_tokens`.
    *   `get_lc_namespace()`: Return `["langchain", "chat_models", "anthropic_bedrock"]`.
*   Ensure serialization with:
    *   LangChain serialization ID as `["langchain", "chat_models", "anthropic_bedrock", "ChatAnthropicBedrock"]`.
    *   `name` as `"ChatAnthropicBedrock"` and `type` as `"constructor"`.
*   Create utility functions in `_bedrock_utils.py`:
    *   `_resolve_aws_credentials`: Unwrap `SecretStr` credentials and return a dictionary, omitting keys with `None` values.
    *   `_create_bedrock_client_params`: Build a parameter dictionary for client initialization, including AWS credentials and configuration settings.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.