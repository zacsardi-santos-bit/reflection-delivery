I'm working on the numaflow platform and I need to add a more meaningful health-check concept to pipelines and inter-step buffer services. Right now, both resource types only have a way to check if they're "ready" based on their conditions, but that doesn't capture the full operational picture.

For pipelines, some phases — like when a pipeline is pausing, already paused, or being deleted — are intentional and normal states. A pipeline in one of these states shouldn't be flagged as unhealthy just because it's not running. I'd like a health check that returns healthy for those transient or intentional phases, only requires the pipeline to also be ready when it's in the running phase, and returns unhealthy for the failed phase or any unrecognized state.

For inter-step buffer services, health should require both the running phase and full readiness. A service that's pending or otherwise not yet running shouldn't be considered healthy even if its conditions happen to be set.

I also need the pipeline status to track map-type and reduce-type user-defined function counts separately, rather than just having a single combined UDF count. The OpenAPI spec should reflect these new fields as well.

Any place in the codebase that currently waits for an inter-step buffer service to be "ready" should be updated to check for the new health status instead.
