## Description

As a super admin, I need a way to debug FHIR search performance by inspecting how a search string is translated into a database query and what execution plan the database produces. Currently, the super admin panel has no interface for running these query plan inspections, and the underlying server endpoint that performs the inspection returns only a single raw output — it doesn't expose the actual SQL query text or its bound parameter values, and it doesn't support selecting between text and structured output formats.

## Expected Behavior

- The super admin panel should include a new form that lets admins enter a FHIR search string and submit it to the explain endpoint.
- The form should have a checkbox to enable a more detailed runtime analysis mode (not just an estimated plan).
- The form should allow scoping the query to a specific project context by selecting a project, a practitioner or patient, and/or a project membership directly. When a project and a person are both selected, the system should automatically resolve the matching project membership.
- On a successful response, the admin should see the generated SQL query, the bound query parameters, and the database execution plan displayed in a modal dialog.
- If an error occurs (e.g., insufficient permissions), the error should be displayed to the user.
- If the user is not a super admin, this section must show an access denied message.
- The server endpoint must now return three outputs: the generated SQL, the parameter bindings, and the plan output. The plan output must be available in both a human-readable text format and a structured JSON format, depending on the requested format.

## Why This Matters

This feature helps super admins diagnose slow or unexpectedly expensive FHIR searches without requiring direct database access. Being able to see the SQL and its execution plan in the admin UI makes troubleshooting much faster and doesn't require deep infrastructure access.
