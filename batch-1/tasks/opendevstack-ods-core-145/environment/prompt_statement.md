I'm working on a Jenkins webhook proxy service and I need to add a couple of capabilities it currently lacks.

Right now the proxy only handles webhook events from BitBucket. I need to add a new HTTP endpoint where other tools or scripts can directly trigger pipeline builds by sending a POST request with a JSON body specifying the branch, project, repository, and optional environment variables. This endpoint should be protected with a shared secret — if the caller doesn't include the right secret as a query parameter, the request should be rejected with an unauthorized status. When the correct secret is provided and the request is valid, the proxy should create the appropriate pipeline configuration in OpenShift and return a success response. There should also be an optional query parameter to override the component name used when deriving the pipeline name.

On top of that, I need all pipeline configurations to support passing environment variables through to the build strategy. Currently the pipeline template doesn't support this. The template should be updated so that environment variables from the request are included in the pipeline strategy configuration, and the generic trigger should be updated to allow environment variable passing as well.

Finally, requests to paths that the proxy doesn't recognize should return a not-found response instead of silently doing nothing.
