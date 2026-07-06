Implement a feature to automatically delete schemas from the schema registry when a broker garbage collects a topic in Apache Pulsar. Ensure that schemas are only removed when the topic is fully deleted due to having no active consumers or subscriptions.

*   Ensure schema deletion occurs during garbage collection for both persistent and non-persistent topics.
*   Prevent garbage collection and schema deletion if:
    *   The topic has at least one active consumer connection.
    *   The topic has existing subscriptions, even if no consumers are currently connected.
*   Implement schema removal when:
    *   All subscriptions on a non-persistent topic are deleted via the admin API.
    *   There are no active consumer connections.
*   Modify the admin API to ensure that deleting a subscription on a non-persistent topic effectively removes the subscription.
*   After schema deletion, ensure querying the schema registry for that topic returns null or indicates the schema is marked as deleted.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.