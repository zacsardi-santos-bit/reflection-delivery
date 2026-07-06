Implement support for generating model classes and API handler code for inline schemas defined inside webhook definitions in OpenAPI 3.1 specifications. Ensure that the generator processes these inline schemas similarly to how it handles inline schemas in regular API paths.

*   Update the inline model resolver to process schemas in the webhooks section of an OpenAPI 3.1 specification.
    *   Ensure it produces named component schemas from inline request body schemas.

*   Modify the generated Java client to include the following classes:
    *   **DefaultApi class** in the `org.openapitools.client.api` package:
        *   Must have a no-arg constructor.
        *   Implement the method `fakeWebhooksSourcesDeletedPost(FakeWebhooksSourcesDeletedPostRequest fakeWebhooksSourcesDeletedPostRequest) throws ApiException`.
    *   **FakeWebhooksSourcesDeletedPostRequest model class** in the `org.openapitools.client.model` package:
        *   Must be instantiatable with a no-arg constructor.
        *   Expose three properties:
            *   `eventTimestamp` of type `OffsetDateTime`.
            *   `eventType` of type `String`.
            *   `event` of type `FakeWebhooksSourcesDeletedPostRequestEvent`.
    *   **FakeWebhooksSourcesDeletedPostRequestEvent model class** in the `org.openapitools.client.model` package:
        *   Must be instantiatable with a no-arg constructor.
        *   Expose one property:
            *   `eventId` of type `String`.

*   Ensure the `petstore.yaml` test resource file at `modules/openapi-generator/src/test/resources/3_1/java/petstore.yaml` includes:
    *   A webhooks section defining the `/fake/webhooks/sources/deleted` POST operation.
    *   An inline request body schema containing:
        *   `event_timestamp` (date-time string).
        *   `event_type` (string).
        *   `event` (object with required `event_id` string property).

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.