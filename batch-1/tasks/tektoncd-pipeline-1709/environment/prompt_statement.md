I'm working on the TaskRun reconciler in the Tekton Pipelines project and I'd like to improve how pods are associated with their parent TaskRun. Right now the reconciler stores the pod's name in the TaskRun's status and later does a direct lookup by that name. This is brittle — if the name isn't set or gets lost, the reconciler can't manage the pod at all.

I'd like to move to a label-based approach: pods created for a TaskRun should be tagged with the TaskRun's name using the standard Tekton label, and the reconciler should discover pods by listing with that label selector rather than fetching by a stored name.

Additionally, when a TaskRun is cancelled, I want the reconciler to actually delete the associated pod (looked up via label), not just update the condition. If no pod exists yet for the cancelled TaskRun, the reconciler should still mark the condition correctly and return without error. If the pod list operation itself fails, that error should be returned from the reconciler.

There's also a related issue with the metrics recorder: when it hasn't been properly initialized, its recording methods should return errors rather than silently doing nothing. The struct should have a boolean field to track initialization state, and all three recording methods should check it.
