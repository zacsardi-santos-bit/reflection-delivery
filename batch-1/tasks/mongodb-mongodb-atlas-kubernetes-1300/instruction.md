Update the Kubernetes operator to be compatible with the new controller framework version. Implement necessary changes to event handler methods and extract utility functions for cache building to improve code reuse and maintainability.

*   Update Event Handler Methods:
    *   Modify the `Create`, `Update`, `Delete`, and `Generic` methods in `pkg/controller/watch/watched_resource_handler.go` to accept `context.Context` as the first parameter.
    *   Update the `Delete` method in `pkg/controller/watch/event_handler.go` to accept `context.Context` as the first parameter.

*   Extract Cache Builder Functions:
    *   Implement `MultiNamespacedCacheBuilder` in `pkg/controller/controller.go` with the signature `MultiNamespacedCacheBuilder(namespaces []string) cache.NewCacheFunc`.
        *   Ensure it configures the cache to watch only the specified namespaces.
    *   Implement `CustomLabelSelectorCacheBuilder` in `pkg/controller/controller.go` with the signature `CustomLabelSelectorCacheBuilder(obj client.Object, labelsSelector labels.Selector) cache.NewCacheFunc`.
        *   Ensure it restricts the cache to resources matching the given label selector for a specific resource type.
    *   Replace existing inline cache builder code with calls to these new functions.

*   Update Unit Tests:
    *   Explicitly register status subresources for `AtlasBackupSchedule`, `AtlasBackupPolicy`, and `AtlasProject` using `WithStatusSubresource` when using a fake Kubernetes client.
    *   Set deletion timestamps on resources after they are created in the fake client to prevent them from being reset.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.