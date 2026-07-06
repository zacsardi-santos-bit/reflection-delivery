I'm working on adding a built-in namespace transformer plugin to kustomize. The goal is to correctly apply a target namespace across a set of Kubernetes resources, but the transformation has several important rules that need to be handled properly.

First, some resource kinds are cluster-scoped and should never have a namespace set on them — things like namespace objects themselves, cluster role bindings, custom resource definitions, persistent volumes, and cluster roles. These should be left completely unchanged when the transformer runs.

Second, for resources like config maps and service accounts that are namespace-scoped, the transformer should set the namespace to the configured target value, creating the field if it's missing or overwriting an existing value.

Third, there's a tricky cross-reference case: when a role binding lists service accounts as subjects, those subject entries include a namespace field. The transformer should update that namespace field, but only for subjects that correspond to service accounts actually being transformed — meaning their name and original namespace match a service account in the resource set. Other subject references (pointing to service accounts outside the transformed set) must be left alone.

Finally, there's an existing bug where processing a role binding that has no subjects field at all causes a panic due to an unsafe type assertion. That needs to be fixed as part of this work.

The plugin should follow the standard kustomize builtin plugin conventions and delegate to the existing namespace transformation logic rather than reimplementing it.
