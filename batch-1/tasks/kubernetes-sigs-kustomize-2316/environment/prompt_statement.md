I'm working on the kustomize project and need to implement a new filter for the filter-based transformation pipeline. Specifically, I need a dedicated replica count filter component for the transformation pipeline.

The filter should take a target resource name and a desired replica count, along with a list of field paths where replicas are configured in a resource. When the filter runs, it should find all resources in the pipeline whose metadata name matches the target name and set each of the specified field paths to the new count. Resources that don't match the name should be left alone.

It also needs to respect a "create if not present" flag on each field path: if the flag is set and the field doesn't exist yet, the field should be created with the new count. If the flag is not set and the field is missing, the resource should remain unchanged. Multiple field paths should all be updatable in one pass.

The filter needs to work as part of a standard resource processing pipeline and should be compatible with the pipeline runner used by the rest of the kustomize filter architecture.
