I'm working on the Ghost admin API and need to add the ability to update an automation's status.

*   The admin API must expose a PUT endpoint at /ghost/api/admin/automations/:id that routes to an 'edit' action, protected by standard admin API authentication (not URL-token auth).

*   The edit endpoint must accept a request body of the form { automations: [{ status, ...otherFields }] } and update only the 'status' field of the automation identified by the URL id parameter; the 'name', 'actions', and 'edges' fields in the request body must be silently ignored.

*   On a successful edit, the endpoint must return HTTP 200 with a response body containing { automations: [fullAutomation] } where the automation object includes: id, name (unchanged), slug, status (updated), created_at, updated_at, actions (unchanged array), and edges (unchanged array). The cache-invalidate header must not be set.

*   The edit endpoint must validate that the status field is one of the values: 'active' or 'inactive'. If the status is an unrecognized non-undefined value, it must return HTTP 422 with a ValidationError response containing: type 'ValidationError', message 'Validation error, cannot edit automation.', property 'status', context 'Automation status must be one of: active, inactive. Received status "<value>".' (where <value> is the invalid status), and help 'Use "active" or "inactive" for automation status.'. The cache-invalidate header must not be set.

*   If the status field is absent from the request body, the endpoint must return HTTP 422 with a ValidationError response containing: type 'ValidationError', message 'Validation error, cannot edit automation.', property 'status', context 'Automation status must be one of: active, inactive.' (no received-value clause), and help 'Use "active" or "inactive" for automation status.'. The cache-invalidate header must not be set.

*   The automations service must expose an 'edit' function that accepts an automation id and a data object containing status, delegates to the repository's edit method with only the status field, and throws a NotFoundError if the repository returns null.

*   The automations repository interface must define an 'edit' method that accepts an id string and a data object with a 'status' field, updates the automation's status and updated_at timestamp in storage, and returns the full updated Automation object (including actions and edges), or null if the automation is not found.


*   Interface details: Type: Route
Name: edit route
Location: ghost/core/core/server/web/api/endpoints/admin/routes.js
Signature: router.put('/automations/:id', mw.authAdminApi, http(api.automations.edit))
Description: Registers the PUT /automations/:id route with standard admin API authentication (not URL-token auth). Must be added alongside the existing GET /automations and GET /automations/:id routes.

Type: Controller Action
Name: edit
Location: ghost/core/core/server/api/endpoints/automations.js
Signature: edit: { headers: { cacheInvalidate: false }, options: ['id'], validation(frame), permissions: true, async query(frame) }
Description: Validates that frame.data.automations[0].status is one of the valid statuses ('active', 'inactive'). If missing or invalid, throws a ValidationError with specific message, context, help, and property fields. On success, delegates to automationsApi.edit(frame.options.id, frame.data.automations[0]).

Validation error format for INVALID (non-undefined) status value:
- message: "Validation error, cannot edit automation."
- context: "Automation status must be one of: active, inactive. Received status \"<value>\"."
- help: "Use \"active\" or \"inactive\" for automation status."
- property: "status"

Validation error format for MISSING (undefined) status:
- message: "Validation error, cannot edit automation."
- context: "Automation status must be one of: active, inactive." (no received-value clause)
- help: "Use \"active\" or \"inactive\" for automation status."
- property: "status"

Type: Function
Name: edit
Location: ghost/core/core/server/services/automations/automations-api.ts
Signature: async function edit(automationId: string, data: { status: string }): Promise<Automation>
Description: Calls repository.edit(automationId, { status: data.status }). If the repository returns null, throws a NotFoundError. Otherwise returns the full Automation object (including unchanged actions and edges).

Type: Method
Name: edit
Location: ghost/core/core/server/services/automations/automations-repository.ts
Signature: edit(id: string, data: Pick<AutomationSummary, 'status'>): Promise<Automation | null>
Description: Must be added to the AutomationsRepository interface. Updates the automation's status and updated_at timestamp. Returns the full Automation object (with actions and edges) or null if not found.

Type: Method
Name: edit
Location: ghost/core/core/server/services/automations/fake-database-automations-repository.ts
Signature: async edit(id: string, data: Pick<AutomationSummary, 'status'>): Promise<Automation | null>
Description: Implementation of the repository edit method. Loads the automation by id, updates its status and updated_at fields in the SQLite database, and returns the full rebuilt Automation object (including actions and edges). Returns null if the automation is not found.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.