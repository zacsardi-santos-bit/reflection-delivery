Implement a new HTTP endpoint in the Jenkins webhook proxy to allow direct pipeline build requests with environment variable support. Ensure the endpoint is protected by a shared secret and update the pipeline configuration to pass environment variables through the build process.

*   Expose a POST endpoint at '/build' in the Server's HandleRoot handler.
    *   Require a 'trigger_secret' query parameter for authorization.
    *   Return HTTP 401 for requests missing the 'trigger_secret'.
*   Handle POST requests to '/build' with:
    *   A valid 'trigger_secret' and JSON body containing 'branch', 'project', 'repository', and 'env' fields.
    *   Return HTTP 200 and create a pipeline BuildConfig via the OpenShift API.
    *   Derive the pipeline name from 'repository-branch' or 'component-branch' if 'component' query parameter is provided.
*   Ensure the BuildConfig JSON structure includes:
    *   'kind': 'BuildConfig', 'apiVersion': 'v1'.
    *   A Generic trigger with 'allowEnv': true.
    *   A JenkinsPipeline strategy with 'jenkinsfilePath': 'Jenkinsfile'.
    *   An 'env' array in jenkinsPipelineStrategy containing environment variable objects from the request payload.
*   Return HTTP 404 for requests to unrecognized paths.
*   Define the EnvPair struct in `jenkins/webhook-proxy/main.go` with:
    *   'Name' field (string, JSON key 'name').
    *   'Value' field (string, JSON key 'value').
*   Define the BuildConfigData struct in `jenkins/webhook-proxy/main.go` to include:
    *   'Env' field of type string for JSON-encoded environment variable pairs.
*   Update the pipeline template in `jenkins/webhook-proxy/pipeline.json.tmpl`:
    *   Include 'allowEnv': true in the Generic trigger section.
    *   Render the Env field as raw JSON under 'env' in the jenkinsPipelineStrategy section using 'text/template'.
*   Implement the getBuildConfig function in `jenkins/webhook-proxy/main.go`:
    *   Accept a *text/template.Template and a BuildConfigData value.
    *   Return the rendered template as a byte slice and an error.
*   Ensure the Event struct in `jenkins/webhook-proxy/main.go` includes:
    *   'Env' field of type []EnvPair.
*   Set the pipelineConfigFilename constant in `jenkins/webhook-proxy/main.go` to "pipeline.json.tmpl".

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.