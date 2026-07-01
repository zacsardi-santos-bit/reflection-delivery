Implement the necessary changes to the webhook proxy service to include the Git repository URI in pipeline configurations and return the response from the build system. Update the relevant interfaces and data structures to support these functionalities.

*   Modify the `Event` struct in `jenkins/webhook-proxy/main.go`:
    *   Add a `GitURI` string field.
    *   Populate `GitURI` by combining the repository base URL, project, and repository name in the format '{RepoBase}/{Project}/{Repo}.git'.

*   Update the `Client` interface in `jenkins/webhook-proxy/main.go`:
    *   Change the `Forward` method signature to `Forward(e *Event, triggerSecret string) ([]byte, error)`.
        *   Ensure it returns the raw response body bytes from the OpenShift API and a nil error on success.
    *   Change the `CreatePipelineIfRequired` method signature to `CreatePipelineIfRequired(tmpl *template.Template, e *Event) error`.
        *   Accept a pre-parsed template as the first parameter.

*   Implement the `ocClient.Forward` method:
    *   Return the full response body from the downstream OpenShift webhook API as a byte slice (`[]byte`).

*   Implement the `getBuildConfig` function in `jenkins/webhook-proxy/main.go`:
    *   Use the signature `getBuildConfig(tmpl *template.Template, e *Event, triggerSecret string) (*bytes.Buffer, error)`.
    *   Render the template with a data struct containing fields `Name` (set to `e.Pipeline`), `TriggerSecret`, `GitURI` (set to `e.GitURI`), and `Branch` (set to `e.Branch`).
    *   Return the rendered `bytes.Buffer`.

*   Ensure the constant `pipelineConfigFilename` in `jenkins/webhook-proxy/main.go` is set to 'pipeline.template.json'.

*   Verify the existence and correct format of the following files:
    *   `jenkins/webhook-proxy/pipeline.template.json`: Use Go template syntax with placeholders `{{.Name}}`, `{{.TriggerSecret}}`, `{{.GitURI}}`, and `{{.Branch}}`.
    *   `jenkins/webhook-proxy/test/fixtures/webhook-triggered-payload.json`: Contains a sample OpenShift build JSON response.
    *   `jenkins/webhook-proxy/test/golden/pipeline.json`: The output of `getBuildConfig` must match this file when called with specific parameters.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.