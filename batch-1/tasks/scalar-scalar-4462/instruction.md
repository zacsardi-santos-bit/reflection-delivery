Implement customizable URL fragment generation for the API reference component. Allow developers to supply custom slug-generation functions for different section types, and update the ID generation functions for models and webhooks to accept structured objects.

*   Update the getModelId function:
    *   Accept an object parameter with a 'name' field.
    *   Return 'model/test-model' for getModelId({ name: 'Test Model' }).
    *   Use 'model/' prepended to the result of a custom generateModelSlug function if provided.

*   Update the getWebhookId function:
    *   Accept a single object parameter with 'name' and optional 'method' fields.
    *   Return 'webhook/POST/test-webhook' for getWebhookId({ name: 'Test Webhook', method: 'POST' }).
    *   Use 'webhook/' prepended to the result of a custom generateWebhookSlug function if provided.

*   Modify useNavState hook:
    *   Internally call useConfig() to access custom slug generator functions.
    *   Ensure ID generation methods use custom generators if present in the configuration.

*   Update getHeadingId function:
    *   Return the result of a custom generateHeadingSlug function directly if provided, without adding any prefix.

*   Update getTagId function:
    *   Use 'tag/' prepended to the result of a custom generateTagSlug function if provided.

*   Extend ReferenceConfiguration type:
    *   Include optional custom slug generator fields:
        *   generateHeadingSlug?: (heading: Heading) => string
        *   generateModelSlug?: (model: { name: string }) => string
        *   generateTagSlug?: (tag: Tag) => string
        *   generateOperationSlug?: (operation: { path: string; operationId: string | undefined; method: string; summary: string | undefined }) => string
        *   generateWebhookSlug?: (webhook: { name: string; method?: string }) => string

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.