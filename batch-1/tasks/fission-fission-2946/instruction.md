Implement unit tests for the message queue trigger manager to ensure it correctly handles subscription lifecycle operations. Verify that the manager accurately tracks active subscriptions, returns correct subscription data, and removes subscriptions when triggers are deleted.

*   Implement the `MakeMessageQueueTriggerManager` function:
    *   Accept parameters: `logger *zap.Logger`, `fissionClient versioned.Interface`, `mqType fv1.MessageQueueType`, `messageQueue messageQueue.MessageQueue`.
    *   Return a pointer to a `MessageQueueTriggerManager` initialized to handle trigger subscription requests.

*   Implement the `MessageQueueTriggerManager` struct:
    *   Include a `service` method that runs as a goroutine to process subscription requests (add, get, delete) using an internal request channel.
    *   Ensure thread-safe operations on trigger subscriptions.

*   Implement the `checkTriggerSubscription` method:
    *   Accept a pointer to `fv1.MessageQueueTrigger`.
    *   Return `false` if no subscription exists for the trigger.

*   Implement the `addTrigger` method:
    *   Accept a pointer to `triggerSubscription`.
    *   Return `nil` error if the subscription is successfully registered.

*   Implement the `getTriggerSubscription` method:
    *   Accept a pointer to `fv1.MessageQueueTrigger`.
    *   Return a non-nil pointer to a `triggerSubscription` with a matching trigger name after addition.

*   Implement the `delTriggerSubscription` method:
    *   Accept a pointer to `fv1.MessageQueueTrigger`.
    *   Remove the corresponding subscription and return `nil` error on success.

*   Ensure `checkTriggerSubscription` returns `true` after a trigger is added and `false` after it is deleted.

*   Define the `triggerSubscription` struct:
    *   Include fields: `trigger fv1.MessageQueueTrigger` and `subscription messageQueue.Subscription`.

*   Ensure the `messageQueue.Subscription` interface can be implemented by custom types, including those wrapping a context with a cancel function.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.