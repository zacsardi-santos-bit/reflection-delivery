I'm working with Tekton pipelines and I've run into two related issues when using task results as inputs to conditions on downstream tasks.

First, when a condition attached to a pipeline task uses a variable expression to reference the result of another task, the pipeline doesn't seem to recognize this as a proper dependency. So the dependent task can get scheduled before the upstream task has even finished producing the result.

Second, even when the upstream task does finish first, the condition parameters still contain the raw placeholder expressions rather than the actual resolved values. It seems like the result value substitution step only covers the task's own parameters and doesn't apply to the parameters of the conditions attached to that task.

I'd expect that if a condition's parameters reference another task's result, the system should automatically treat that as an execution dependency (so the task runs in the right order), and that those placeholder expressions should be replaced with the real values when the pipeline is running. Could you fix both of these gaps so that conditions can properly receive and use results from upstream tasks?
