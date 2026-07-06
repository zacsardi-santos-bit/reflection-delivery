I'm working on adding support for AI content generation blocking triggers in the Firebase CLI deployment flow.

*   The AILogicService.validateTrigger method must throw an error with the message 'Can only create at most one regional AI Logic Trigger for {eventType} in region {region}' when two or more regional blocking triggers (those with options.regionalWebhook set to true) share the same event type and the same region.

*   The AILogicService.validateTrigger method must NOT throw when two regional blocking triggers share the same event type but are in different regions.

*   The AILogicService.validateTrigger method must throw an error with the message 'Can only create at most one global AI Logic Trigger for {eventType}' when two or more global blocking triggers (those without options.regionalWebhook) share the same event type.

*   The AILogicService.validateTrigger method must NOT throw when one regional and one global blocking trigger share the same event type.

*   The AILogicService.validateTrigger method must NOT throw when two blocking triggers have different event types, regardless of region or regional/global scope.

*   The AILogicService.registerTrigger method must call upsertBlockingFunction from src/gcp/ailogic with the endpoint as the sole argument.

*   The AILogicService.unregisterTrigger method must call deleteBlockingFunction from src/gcp/ailogic with the endpoint as the sole argument.

*   The upsertBlockingFunction must POST to 'projects/{project}/locations/global/triggers' (for global triggers) with body { cloudFunction: { id: endpoint.id, locationId: endpoint.region } } and query params { triggerId: '<trigger-id>', validateOnly: 'false' }. For beforeGenerateContent, triggerId is 'before-generate-content'; for afterGenerateContent, triggerId is 'after-generate-content'.

*   When the POST in upsertBlockingFunction receives an HTTP 409 response, it must fall back to PATCH on 'projects/{project}/locations/{region}/triggers/{trigger-id}' (using the endpoint region for regional triggers, or 'global' for global triggers) with body { cloudFunction: { id: endpoint.id, locationId: endpoint.region } } and query params { allowMissing: 'false', validateOnly: 'false' }.

*   When the POST in upsertBlockingFunction receives a non-409 error response (e.g. 500), it must throw that error and must NOT call PATCH.

*   The deleteBlockingFunction must send DELETE to 'projects/{project}/locations/global/triggers/{trigger-id}' (for global triggers) or 'projects/{project}/locations/{region}/triggers/{trigger-id}' (for regional triggers) with query params { allowMissing: 'true', validateOnly: 'false' }.

*   The src/gcp/ailogic module must export a named 'client' HTTP client instance that has post, patch, and delete methods used for making AI Logic API requests.

*   The src/functions/events/v2 module must export the constant AI_LOGIC_BEFORE_GENERATE_CONTENT with the value 'firebase.vertexai.v1beta.beforeGenerateContent'.

*   The src/functions/events/v2 module must export the constant AI_LOGIC_AFTER_GENERATE_CONTENT with the value 'firebase.vertexai.v1beta.afterGenerateContent'.


*   Interface details: Type: Class
Name: AILogicService
Location: src/deploy/functions/services/ailogic.ts
Description: Service class for managing AI Logic blocking triggers during Firebase function deployment.
Signature:
  validateTrigger(endpoint: backend.Endpoint, allEndpoints: backend.Backend): void
  registerTrigger(endpoint: backend.Endpoint): Promise<void>
  unregisterTrigger(endpoint: backend.Endpoint): Promise<void>

Type: Function
Name: upsertBlockingFunction
Location: src/gcp/ailogic.ts
Signature: upsertBlockingFunction(endpoint: Endpoint): Promise<void>
Description: Creates a new AI Logic blocking trigger via POST. If the POST fails with HTTP 409 (conflict), falls back to PATCH to update the existing trigger. For any other POST failure, throws the error without calling PATCH. Global triggers (no regionalWebhook option) use "locations/global"; regional triggers use "locations/{region}".

Type: Function
Name: deleteBlockingFunction
Location: src/gcp/ailogic.ts
Signature: deleteBlockingFunction(endpoint: Endpoint): Promise<void>
Description: Deletes an AI Logic blocking trigger. Global triggers use "locations/global"; regional triggers use "locations/{region}". Uses allowMissing: "true" so deletion is idempotent.

Type: Export
Name: client
Location: src/gcp/ailogic.ts
Description: An exported HTTP API client instance with post, patch, and delete methods used to communicate with the AI Logic API.

Type: Constant
Name: AI_LOGIC_BEFORE_GENERATE_CONTENT
Location: src/functions/events/v2.ts
Description: Event type constant for the "before generate content" AI Logic blocking trigger. Value: "firebase.vertexai.v1beta.beforeGenerateContent". Maps to trigger ID "before-generate-content" in API calls.

Type: Constant
Name: AI_LOGIC_AFTER_GENERATE_CONTENT
Location: src/functions/events/v2.ts
Description: Event type constant for the "after generate content" AI Logic blocking trigger. Value: "firebase.vertexai.v1beta.afterGenerateContent". Maps to trigger ID "after-generate-content" in API calls.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.