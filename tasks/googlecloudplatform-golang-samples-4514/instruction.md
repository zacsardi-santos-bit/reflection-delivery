Implement Go sample code to demonstrate the use of the Vertex AI model evaluation service for three common scenarios. Create standalone functions for ROUGE scoring, single-model response evaluation, and pairwise model comparison, each connecting to the appropriate regional endpoint and handling errors gracefully.

*   Implement a function named `getROUGEScore`:
    *   Accepts parameters: `io.Writer`, `projectID` string, `location` string.
    *   Connects to the Vertex AI evaluation service endpoint at `{location}-aiplatform.googleapis.com:443`.
    *   Executes a ROUGE metric evaluation.
    *   Writes results to the provided writer.
    *   Returns `nil` on success or a non-nil error on failure.

*   Implement a function named `evaluateModelResponse`:
    *   Accepts parameters: `io.Writer`, `projectID` string, `location` string.
    *   Connects to the Vertex AI evaluation service.
    *   Evaluates a model response using a single-instance metric (e.g., groundedness).
    *   Writes the score, confidence, and explanation to the provided writer.
    *   Returns `nil` on success or a non-nil error on failure.

*   Implement a function named `pairwiseEvaluation`:
    *   Accepts parameters: `io.Writer`, `projectID` string, `location` string.
    *   Connects to the Vertex AI evaluation service.
    *   Performs a pairwise comparison between two model responses.
    *   Writes the winning choice, confidence, and explanation to the provided writer.
    *   Returns `nil` on success or a non-nil error on failure.

*   Ensure all functions reside in a Go package named `evaluation`:
    *   Located at `vertexai/evaluation/` within the repository.
    *   Include a `go.mod` file declaring the module `github.com/GoogleCloudPlatform/golang-samples/vertexai/evaluation`.

*   Construct the API endpoint URL using the format `{location}-aiplatform.googleapis.com:443` for all functions.

*   Handle errors by returning a non-nil error if client creation or the evaluation API call fails, and return `nil` otherwise.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.