Implement support for the OpenAI Batch API in the Kotlin client library. Create data types for batch jobs, requests, and outputs, and add methods to manage batch operations such as creation, retrieval, listing, and cancellation.

*   Implement the `Batch` data class in `com.aallam.openai.api.batch`.
    *   Include fields: `id` (BatchId), `endpoint` (Endpoint), `completionWindow` (CompletionWindow?).
    *   Ensure JSON deserialization compatibility.

*   Create the `BatchId` inline/value class in `com.aallam.openai.api.batch`.
    *   Wrap a String, accessible via `id`.

*   Define the `Endpoint` inline/value class in `com.aallam.openai.api.core`.
    *   Wrap a String, accessible via `path`.
    *   Include companion object constant `Completions` as `Endpoint("/v1/chat/completions")`.

*   Develop the `CompletionWindow` inline/value class in `com.aallam.openai.api.batch`.
    *   Wrap a String, accessible via `value`.
    *   Include companion object constant `TwentyFourHours` with value "24h".

*   Implement the `CustomId` inline/value class in `com.aallam.openai.api.batch`.
    *   Ensure JSON serialization and equality for instances with the same String.

*   Create the `RequestOutput` data class in `com.aallam.openai.api.batch`.
    *   Fields: `customId` (CustomId), `response` (ResponseOutput?).
    *   Ensure JSON deserialization compatibility.

*   Define the `ResponseOutput` data class in `com.aallam.openai.api.batch`.
    *   Fields: `statusCode` (Int), `body` (JsonObject).
    *   Ensure `body` is decodable into a `ChatCompletion`.

*   Implement the `RequestInput` data class in `com.aallam.openai.api.batch`.
    *   Fields: `customId` (CustomId), `method` (Method), `url` (String), `body` (ChatCompletionRequest?).
    *   Ensure JSON serialization compatibility.

*   Develop the `Method` inline/value class in `com.aallam.openai.api.batch`.
    *   Include companion object constant `Post` for HTTP POST method.

*   Create the `BatchRequest` data class in `com.aallam.openai.api.batch`.
    *   Fields: `inputFileId` (FileId), `endpoint` (Endpoint), `completionWindow` (CompletionWindow).

*   Implement the `batchRequest` DSL builder function in `com.aallam.openai.api.batch`.
    *   Expose properties: `inputFileId`, `endpoint`, `completionWindow`.

*   Extend the OpenAI client with batch operations in `com.aallam.openai.client`.
    *   Methods: 
        *   Create a batch: `suspend fun batch(request: BatchRequest, requestOptions: RequestOptions? = null): Batch`.
        *   Retrieve a batch by ID: `suspend fun batch(id: BatchId, requestOptions: RequestOptions? = null): Batch?`.
        *   List all batches: `suspend fun batches(after: BatchId? = null, limit: Int? = null, requestOptions: RequestOptions? = null): List<Batch>`.
        *   Cancel a batch: `suspend fun cancel(id: BatchId, requestOptions: RequestOptions? = null): Batch?`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.