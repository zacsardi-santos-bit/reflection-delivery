Implement a Google Cloud Pub/Sub message queue integration for the ActivityPub service to enable asynchronous processing of federation activities. Ensure the system can publish messages, handle incoming messages, and verify topic and subscription existence during initialization.

*   Implement the `GCloudPubSubMessageQueue` class in `src/mq/gcloud-pubsub-mq.ts`.
    *   Constructor must accept: `PubSub` client, `EventEmitter` event bus, `Logger`, `topicIdentifier` string, `subscriptionIdentifier` string, and `messageReceivedEventName` string.
    *   `enqueue` method:
        *   Publish messages as JSON with a 'fedifyId' attribute.
        *   Skip publishing if a delay is set.
        *   Propagate errors if publishing fails.
    *   `listen` method:
        *   Register a listener for the configured event name.
        *   Remove the listener when the provided `AbortSignal` is aborted.
        *   On receiving `MqMessageReceivedEvent`, handle message data and invoke `ack` or `nack` based on success or failure.

*   Implement the `MqMessageReceivedEvent` class in `src/events/mq-message-received-event.ts`.
    *   Constructor must accept: `id`, `subscriptionIdentifier`, `data`, `attributes`, `onAck`, and `onNack`.
    *   Expose `data` property and provide `ack()` and `nack()` methods.

*   Implement the `initGCloudPubSubMessageQueue` function in `src/helpers/gcloud-pubsub-mq.ts`.
    *   Accept `Logger`, `EventEmitter`, `eventName`, and an `options` object with `topicName`, `subscriptionName`, and optional `host`, `emulatorMode`, `projectId`.
    *   Construct `PubSub` client using only explicitly defined options.
    *   Validate topic existence; throw "Topic does not exist: <topicName>" if missing.
    *   Validate subscription existence; throw "Subscription does not exist: <subscriptionName>" if missing.
    *   Return a configured `GCloudPubSubMessageQueue` instance.

*   Implement the `getFullTopicIdentifier` function in `src/helpers/gcloud-pubsub-mq.ts`.
    *   Accept `projectId` and `topicName`.
    *   Return full topic identifier in the format "projects/<projectId>/topics/<topicName>".

*   Implement the `getFullSubscriptionIdentifier` function in `src/helpers/gcloud-pubsub-mq.ts`.
    *   Accept `projectId` and `subscriptionName`.
    *   Return full subscription identifier in the format "projects/<projectId>/subscriptions/<subscriptionName>".

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.