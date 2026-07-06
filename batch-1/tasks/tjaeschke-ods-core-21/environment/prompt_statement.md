I'm working on a webhook proxy service that sits between a source control system and a build platform. There are two issues I need to fix.

First, when the proxy creates a new pipeline configuration in the build system, it doesn't include the Git repository URI in the configuration. This means the build system doesn't know where to find the source code. The URI should be derived from the incoming event data — combining a base URL, the project name, and the repository name — and then included in the generated pipeline configuration. I'd like to switch from the current simple string-substitution approach for generating this configuration to a proper template-based approach, where named placeholders are filled in for the pipeline name, trigger secret, Git URI, and branch.

Second, when the proxy forwards a webhook event to the build system and triggers a build, it currently discards whatever response comes back from the build system. I need the forwarding operation to capture and return that response body so it can be passed back to the original caller.

These two changes require updating the relevant interfaces and data structures: the event data structure needs to carry the Git URI, the pipeline-creation function needs to accept a pre-parsed template, and the forwarding function needs to return the response bytes alongside any error.
