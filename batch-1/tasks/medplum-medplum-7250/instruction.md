Implement a new section in the Medplum super admin panel to allow super admins to inspect FHIR search queries and their corresponding database execution plans. Ensure the interface supports detailed runtime analysis and context scoping by project and user. Update the server endpoint to return SQL queries, parameter bindings, and execution plans in both text and JSON formats.

*   Update the `SuperAdminPage` to render the `ExplainSearchForm` component.
    *   Include a required text input labeled 'Search *'.
    *   Include a checkbox labeled 'Analyze' to toggle runtime analysis mode.
    *   Include reference inputs with placeholders 'Project', 'Practitioner or Patient', and 'ProjectMembership'.
    *   Include a submit button labeled 'Explain Search'.
*   Ensure the form behavior:
    *   Do not call the POST endpoint if the 'Search *' input is empty.
    *   On submit, call `medplum.post` with the URL `fhir/R4/$explain` and a body containing `{ query, analyze, format: 'text' }`.
    *   Set the 'analyze' field in the POST body based on the 'Analyze' checkbox state.
    *   Include the header `x-medplum-on-behalf-of` with value `ProjectMembership/{id}` if a ProjectMembership is selected.
    *   Resolve and use the appropriate ProjectMembership when both a Project and a Practitioner or Patient are selected.
*   Handle the response:
    *   Display a modal titled 'Database Explain' with the SQL query, parameters, and execution plan on success.
    *   Show error messages (e.g., 'Forbidden') if the endpoint returns an error.
    *   Display a 'Forbidden' message instead of the form if the user is not a super admin.
*   Update the server-side `dbExplainHandler` in `packages/server/src/fhir/operations/explain.ts`:
    *   Accept input parameters: `query` (string, required), `analyze` (boolean, optional), `format` (string, optional — 'json' or 'text').
    *   Return three output parameters: `query`, `parameters`, and `explain`.
    *   Ensure the `explain` output contains a JSON object starting with '{"Plan":' when format is 'json'.
    *   Ensure the `explain` output contains text with '(cost=' when format is 'text'.
    *   Ensure the `query` output contains the SQL SELECT statement, and `parameters` output contains bound parameters in '$1 = ' format.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.