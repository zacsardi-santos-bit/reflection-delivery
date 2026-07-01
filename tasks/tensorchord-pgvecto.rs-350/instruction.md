Create a new workspace crate to handle HTTP communication with external embedding API services. Implement functions and structures to send requests, parse responses, and handle errors for embedding operations. Ensure robust error handling for various failure modes.

*   Create a new 'embedding' workspace crate at `crates/embedding/` and register it in the workspace `Cargo.toml`:
    *   Add `embedding = { path = "crates/embedding" }` to the workspace members.
    *   Declare dependencies in `embedding` crate's `Cargo.toml`:
        *   `reqwest` version "0.11" with features: ["blocking", "json", "rustls-tls"]
        *   `thiserror` version "~1.0"
        *   `serde` version "~1.0"
        *   Dev-dependencies: `httpmock` version "0.7", `serde_json` version "~1.0"

*   Implement the `openai_embedding` function in `crates/embedding/src/lib.rs`:
    *   Signature: `openai_embedding(input: String, model: String, opt: OpenAIOptions) -> Result<EmbeddingResponse, EmbeddingError>`
    *   Send a POST request to `{opt.base_url}/embeddings` with an Authorization Bearer header using `opt.api_key`.
    *   Serialize the request body as form data.
    *   Parse the JSON response into an `EmbeddingResponse`.
    *   Return `Err(EmbeddingError)` if the HTTP request fails or the response cannot be parsed as JSON.

*   Define `OpenAIOptions` struct in `crates/embedding/src/lib.rs`:
    *   Fields:
        *   `pub base_url: String`
        *   `pub api_key: String`

*   Define `EmbeddingError` struct in `crates/embedding/src/openai.rs`:
    *   Implement the Error trait via `thiserror`.
    *   Display format: "Error happens at embedding.\nINFORMATION: hint = {hint}"
    *   Field:
        *   `pub hint: String`

*   Define `EmbeddingData` struct in `crates/embedding/src/openai.rs`:
    *   Derive Debug, Deserialize, Serialize.
    *   Fields:
        *   `pub object: String`
        *   `pub embedding: Vec<f32>`
        *   `pub index: i32`

*   Define `EmbeddingRequest` struct in `crates/embedding/src/openai.rs`:
    *   Derive Debug, Serialize, Clone.
    *   Fields:
        *   `pub model: String`
        *   `pub input: String`
        *   `pub dimensions: Option<i32>` (skip_serializing_if = "Option::is_none")
        *   `pub user: Option<String>` (skip_serializing_if = "Option::is_none")
    *   Constructor: `new(model: String, input: String) -> Self` (sets dimensions and user to None)

*   Define `EmbeddingResponse` struct in `crates/embedding/src/openai.rs`:
    *   Derive Debug, Deserialize, Serialize.
    *   Fields:
        *   `pub object: String`
        *   `pub data: Vec<EmbeddingData>`
        *   `pub model: String`
        *   `pub usage: Usage`
    *   Method: `try_pop_embedding(mut self) -> Result<Vec<f32>, EmbeddingError>`
        *   Returns `Ok(Vec<f32>)` if data is non-empty.
        *   Returns `Err(EmbeddingError { hint: "no embedding from service".to_string() })` if data is empty.

*   Define `Usage` struct in `crates/embedding/src/openai.rs`:
    *   Derive Debug, Deserialize, Serialize.
    *   Fields:
        *   `pub prompt_tokens: i32`
        *   `pub total_tokens: i32`

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.