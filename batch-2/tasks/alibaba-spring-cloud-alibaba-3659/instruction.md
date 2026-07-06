Implement the `TongYiChatOptions` class to encapsulate configuration options for the AI chat integration module in Spring Cloud Alibaba. Ensure the class supports a builder pattern, allowing developers to create a configuration object with default values by calling `build()` with no arguments.

*   Implement the `TongYiChatOptions` class in the specified location:
    *   File path: `spring-cloud-alibaba-starters/spring-cloud-starter-alibaba-ai/src/main/java/com/alibaba/cloud/ai/tongyi/TongYiChatOptions.java`
    *   Ensure it implements the `ChatOptions` interface from Spring AI.

*   Provide a static `builder()` method in `TongYiChatOptions`:
    *   Returns an instance of the inner `Builder` class.
    *   Signature: `static Builder builder()`

*   Implement the `Builder` inner class within `TongYiChatOptions`:
    *   Must be constructable with no arguments.
    *   Provide a `build()` method that returns a non-null `TongYiChatOptions` instance.
    *   Signature: `TongYiChatOptions build()`

*   Ensure default configuration behavior:
    *   When `TongYiChatOptions` is constructed using the builder with no configuration calls (i.e., `TongYiChatOptions.builder().build()`), the resulting object must be non-null.
    *   The default value of the `maxTokens` field must be 1500.
    *   Implement `getMaxTokens()` to return the `maxTokens` value, defaulting to 1500 if not explicitly set.
    *   Signature: `Integer getMaxTokens()`

*   Provide a method to set the `maxTokens` value:
    *   Implement `setMaxTokens(Integer maxTokens)` to allow explicit configuration of the token limit.
    *   Signature: `void setMaxTokens(Integer maxTokens)`

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.