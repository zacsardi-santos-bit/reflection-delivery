## Description

The langchain-anthropic library currently only supports using Claude models via the direct Anthropic API. There is no built-in way to use Claude models deployed through AWS Bedrock, which many organizations prefer for regional compliance, IAM-based authentication, and enterprise security requirements.

We need a first-class chat model class for AWS Bedrock that provides the same interface as the existing Anthropic chat model — including tool binding, structured output, and streaming support — but routes requests through Bedrock. It should handle AWS credentials naturally: accepting them as explicit parameters (stored securely) or reading them from the standard AWS environment variables. Region selection should also work the same way, with the explicit parameter taking priority over environment variables.

## Expected Behavior

- A new chat model class is available at the top level of the package, allowing it to be used as a drop-in alternative for Bedrock-hosted Claude.
- AWS credentials (access key, secret key, session token) can be passed directly or picked up from standard environment variables automatically.
- The AWS region can be set explicitly or inferred from the environment, with an explicit value always winning.
- The class correctly reports its provider identity in LangSmith tracing (as a Bedrock provider, not the direct API).
- The class is properly serializable with a stable LangChain namespace for use in chains and agents.
- Model name resolution handles the various formats that appear in Bedrock (short names, vendor-prefixed IDs, and cross-region inference profile IDs).
- A utility module exposes helper functions for building Bedrock client parameters and resolving AWS credentials from optional secret values.

## Why This Matters

Many enterprise and regulated environments route all AI API calls through AWS Bedrock for security, auditing, and data residency reasons. Without native support in langchain-anthropic, these users must maintain custom wrappers or use less well-integrated alternatives.
