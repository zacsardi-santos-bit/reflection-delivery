Update the promotions controller's event handler methods to use the new typed work queue interface. Ensure that all methods correctly enqueue reconcile requests as before, adapting only the type of the work queue parameter.

*   Modify all event handler methods in `internal/controller/promotions/watches.go`:
    *   Change the work queue parameter to `workqueue.TypedRateLimitingInterface[reconcile.Request]` for:
        *   EnqueueHighestPriorityPromotionHandler:
            *   `Create(ctx context.Context, evt event.TypedCreateEvent[T], wq workqueue.TypedRateLimitingInterface[reconcile.Request])`
            *   `Delete(ctx context.Context, evt event.TypedDeleteEvent[T], wq workqueue.TypedRateLimitingInterface[reconcile.Request])`
            *   `Generic(ctx context.Context, evt event.TypedGenericEvent[T], wq workqueue.TypedRateLimitingInterface[reconcile.Request])`
            *   `Update(ctx context.Context, evt event.TypedUpdateEvent[T], wq workqueue.TypedRateLimitingInterface[reconcile.Request])`
            *   `enqueueNext(stageKey types.NamespacedName, wq workqueue.TypedRateLimitingInterface[reconcile.Request])`
        *   UpdatedArgoCDAppHandler:
            *   `Create(ctx context.Context, evt event.TypedCreateEvent[T], wq workqueue.TypedRateLimitingInterface[reconcile.Request])`
            *   `Delete(ctx context.Context, evt event.TypedDeleteEvent[T], wq workqueue.TypedRateLimitingInterface[reconcile.Request])`
            *   `Generic(ctx context.Context, evt event.TypedGenericEvent[T], wq workqueue.TypedRateLimitingInterface[reconcile.Request])`
            *   `Update(ctx context.Context, e event.TypedUpdateEvent[T], wq workqueue.TypedRateLimitingInterface[reconcile.Request])`
*   Ensure the UpdatedArgoCDAppHandler.Update method enqueues items correctly:
    *   Enqueue 0 items if the event has no new object.
    *   Enqueue 0 items if the event has no old object.
    *   Enqueue 1 item when the ArgoCD Application has exactly one indexed Promotion.
    *   Enqueue 2 items when the ArgoCD Application has multiple indexed Promotions.
    *   Enqueue 0 items if an indexed Promotion exists but the shard selector does not match.
    *   Enqueue 0 items if the Application has no indexed Promotion.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.