## Description

Currently, the Grafana integration in Flagsmith can only be configured at the project level. This means that if an organization has multiple projects, each project needs its own Grafana integration configuration. There is no way to set up a single Grafana integration at the organization level that applies across all projects and environments.

Additionally, audit log entries don't have a direct way to resolve which organization they belong to, which limits routing of audit events to organization-level integrations. When an audit log is associated only with an environment or an author (rather than directly with a project), there's no mechanism to walk up the relationship chain to find the associated organization.

## Expected Behavior

- Administrators should be able to create, update, retrieve, and delete a Grafana integration configuration at the organization level through a dedicated API endpoint.
- Only one Grafana configuration can exist per organization — attempting to create a second one should be rejected.
- Non-admin users should be denied access (with a 403 response) to all organization-level Grafana configuration endpoints.
- Audit log entries should expose a way to determine their associated organization by traversing their related objects (project, environment, author, etc.), returning nothing if no organization can be found.
- When sending audit events to Grafana, the system should look up the organization-level Grafana configuration as a fallback when no project-level configuration exists.
- There should be a dedicated signal handler for forwarding audit log events to Dynatrace, parallel to the existing Grafana handler.
- The API documentation should cover the schema in both JSON and YAML formats, in addition to the Swagger UI.

## Why This Matters

Teams using Flagsmith at scale often have many projects under a single organization and want to configure monitoring integrations (like Grafana) once, centrally, rather than repeating configuration on every project. This organization-level integration support reduces operational overhead and enables more consistent audit event tracking across the entire organization.
