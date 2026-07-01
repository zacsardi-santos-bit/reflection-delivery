Update the Google Cloud Pub/Sub Go client library to properly release it as a new major version. Ensure that all internal imports in the v2 module reference the correct versioned paths, rename specific types to reflect their roles accurately, and update the tracing instrumentation to use the v2 module's name.

*   Configure the pubsub v2 module with the correct Go module path:
    *   Update all internal package imports in `pubsub/v2/*.go` files to use versioned v2 paths (`cloud.google.com/go/pubsub/v2/apiv1`, `cloud.google.com/go/pubsub/v2/apiv1/pubsubpb`).
*   Modify the package at `cloud.google.com/go/pubsub/v2/apiv1`:
    *   Export a struct type `TopicAdminCallOptions` with a `Publish` field of type `[]gax.CallOption`.
    *   Export a struct type `SubscriptionAdminCallOptions`.
    *   Export a client type `TopicAdminClient` and a constructor function `NewTopicAdminClient(ctx context.Context, opts ...option.ClientOption) (*TopicAdminClient, error)`.
    *   Export a client type `SubscriptionAdminClient` and a constructor function `NewSubscriptionAdminClient(ctx context.Context, opts ...option.ClientOption) (*SubscriptionAdminClient, error)`.
*   Update all references in non-test code within `pubsub/v2/`:
    *   Replace v1 client types (`PublisherClient`, `SubscriberClient`, `PublisherCallOptions`, `SubscriberCallOptions`, `NewPublisherClient`, `NewSubscriberClient`) with v2 type names (`TopicAdminClient`, `SubscriptionAdminClient`, `TopicAdminCallOptions`, `SubscriptionAdminCallOptions`, `NewTopicAdminClient`, `NewSubscriptionAdminClient`).
*   Ensure the OpenTelemetry instrumentation scope name:
    *   Set the constant `defaultTracerName` in `pubsub/v2/trace.go` to "cloud.google.com/go/pubsub/v2".
*   Update package comments:
    *   Ensure comments in `pubsub/v2/doc.go` and `pubsub/v2/pubsub.go` reference the import path "cloud.google.com/go/pubsub/v2".

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.