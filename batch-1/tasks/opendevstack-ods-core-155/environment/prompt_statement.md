I'm working on a webhook proxy service that receives build requests and forwards them to an orchestration system. Right now, the service has some gaps in error handling that I need to fix.

First, if the incoming request body is malformed and can't be parsed as JSON, the service should return a 400 status with a message saying it couldn't parse the JSON, instead of silently proceeding with bad data.

Second, even if the JSON is well-formed, the payload can still be semantically invalid — for example, if required fields like the branch name are empty. In that case, the service should return 400 with an appropriate invalid input message rather than forwarding an invalid event downstream.

Third — and this is the trickier one — when the downstream system rejects a pipeline creation request (for instance, returning a 422), that status code needs to be propagated back to the original caller along with a meaningful error message. Currently the error is swallowed and the caller gets no useful feedback.

Finally, the behavior around missing or incorrect trigger secrets should ensure that no event processing happens at all — not just that a 401 is returned, but that the pipeline creation logic is never invoked.

The pipeline creation function's interface needs to be updated so it returns a status code alongside any error, enabling the handler to propagate the correct status back to callers.
