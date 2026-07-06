Implement a new Terraform resource to manage custom HTML content for Auth0 Universal Login prompt screens. Ensure the resource supports full lifecycle operations: creation, reading, updating, and deletion of custom HTML sections. Handle unset sections as empty strings.

*   Create a new Terraform resource type named `auth0_prompt_partials`.
    *   Register this resource in `internal/provider/provider.go`.

*   Define the resource schema in `internal/auth0/prompt/resource_partials.go` with the following attributes:
    *   `prompt` (string, Required): Identifies the prompt screen to customize. Acceptable values include "login", "login-id", "login-password", "signup", "signup-id", "signup-password".
    *   `form_content_start` (string, Optional): HTML for the start of the form. Defaults to an empty string.
    *   `form_content_end` (string, Optional): HTML for the end of the form. Defaults to an empty string.
    *   `form_footer_start` (string, Optional): HTML for the start of the footer. Defaults to an empty string.
    *   `form_footer_end` (string, Optional): HTML for the end of the footer. Defaults to an empty string.
    *   `secondary_actions_start` (string, Optional): HTML for the start of secondary actions. Defaults to an empty string.
    *   `secondary_actions_end` (string, Optional): HTML for the end of secondary actions. Defaults to an empty string.

*   Implement the resource lifecycle operations:
    *   On create, set the resource ID to the value of the `prompt` attribute.
    *   On read, call `GET /api/v2/prompts/{prompt}/partials` to populate all six partial attributes.
    *   On create and update, call `PUT /api/v2/prompts/{prompt}/partials` with a JSON body containing the six partial fields using hyphenated names.
    *   On delete, call `PUT /api/v2/prompts/{prompt}/partials` with a body of `{"<prompt>":{}}` to clear all partials.

*   Export the constructor function `NewPartialsResource()` from `internal/auth0/prompt/resource_partials.go`.
    *   Ensure it returns the `schema.Resource` definition for the `auth0_prompt_partials` resource.
    *   Register this function in `internal/provider/provider.go` under the key `auth0_prompt_partials`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.