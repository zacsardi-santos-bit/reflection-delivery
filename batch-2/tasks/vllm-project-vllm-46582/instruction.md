I'm running into an issue with the vLLM Rust server rejecting chat completion requests when the request body is large.

*   The build_router function must configure the HTTP router with a maximum request body size of 32 MiB (32 × 1024 × 1024 bytes), overriding the web framework's built-in default body size limit.

*   The /v1/chat/completions POST endpoint must return HTTP 200 OK for valid requests whose JSON body size exceeds the framework's default body limit (approximately 2 MiB) but is within the configured 32 MiB limit.


*   Interface details: Type: Function
Name: build_router
Location: rust/src/server/src/routes.rs
Signature: build_router(state: Arc<AppState>) -> Router
Description: Builds and returns the Axum HTTP router for the vLLM server. This function must apply a DefaultBodyLimit layer with a maximum of 32 MiB (32 * 1024 * 1024 bytes) to override Axum's default 2 MiB body size limit, ensuring that all routes — including POST /v1/chat/completions — accept request bodies up to 32 MiB.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.