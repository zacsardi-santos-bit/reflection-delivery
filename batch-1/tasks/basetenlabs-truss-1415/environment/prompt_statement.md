I'm working on cleaning up our model deployment API. Right now, every method that creates or deploys a model or chain requires callers to pass along a client version string and a trust flag. These feel like internal concerns that callers shouldn't have to know about — they just want to deploy something. I'd like to remove those parameters from all the deployment-related methods and instead have the system automatically collect and send client environment information with each request.

While doing this cleanup, I also want to switch the underlying API transport from form-encoded bodies to standard JSON, so the GraphQL query and any variables are sent as a proper JSON object. For chain deployments specifically, the GraphQL mutation should accept the environment context as a query variable rather than interpolating it as a string directly into the mutation.

A couple of other things to clean up at the same time: a Python version resolution helper that's currently part of the public module interface should be made private (it's an internal utility, not something external code should depend on). And a legacy plugin configuration option for a model inference acceleration framework should be removed since it's no longer needed.

Can you help implement all of these changes?
