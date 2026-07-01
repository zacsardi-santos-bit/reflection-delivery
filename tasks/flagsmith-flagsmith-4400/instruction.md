Implement an organization-level Grafana integration configuration in Flagsmith. Enable administrators to manage this configuration through a dedicated API endpoint, ensuring only one configuration per organization. Update audit log handling to determine associated organizations and route events accordingly.

*   Rename the GrafanaConfiguration model to GrafanaProjectConfiguration in `api/integrations/grafana/models.py`.
    *   Update all references in views, serializers, URL configs, and signals to use GrafanaProjectConfiguration.

*   Create a new GrafanaOrganisationConfiguration model in `api/integrations/grafana/models.py`.
    *   Extend IntegrationsModel.
    *   Include a OneToOneField to Organisation with `related_name='grafana_config'`.
    *   Add `base_url` (URLField) and `api_key` (CharField) fields.

*   Register a new REST API endpoint for GrafanaOrganisationConfiguration under the organisations router.
    *   Path: 'integrations/grafana'.
    *   URL names: 'api-v1:organisations:integrations-grafana-list' (list) and 'api-v1:organisations:integrations-grafana-detail' (detail).

*   Implement the GrafanaOrganisationConfigurationViewSet in `api/integrations/grafana/views.py`.
    *   Enforce admin-only access: Admin users have full CRUD access; non-admin users receive HTTP 403 Forbidden.
    *   On POST, if no existing configuration for the organization, return HTTP 201 Created and persist the configuration.
    *   On POST, if a configuration already exists, return HTTP 400 Bad Request.
    *   On PUT, update the configuration and return HTTP 200 OK.
    *   On DELETE, remove the configuration and return HTTP 204 No Content.

*   Update the AuditLog model in `api/audit/models.py`.
    *   Add an `organisation` property to resolve the associated organization by checking related objects in order: project, environment, author, master_api_key, history_record.
    *   Return None if no organization can be resolved.

*   Modify the send_audit_log_event_to_grafana function in `api/audit/signals.py`.
    *   Use organization-level Grafana configuration as a fallback if no project-level configuration exists.
    *   Instantiate GrafanaWrapper with base_url and api_key from the configuration.
    *   Call generate_event_data and track_event_async with the audit log instance.

*   Implement the send_audit_log_event_to_dynatrace function in `api/audit/signals.py`.
    *   When an audit log's environment has a DynatraceConfiguration, instantiate DynatraceWrapper with base_url, api_key, and entity_selector.
    *   Call generate_event_data and track_event_async with the audit log instance.

*   Ensure API schema documentation endpoints return HTTP 200 OK for both authenticated and unauthenticated clients.
    *   Include endpoints for JSON schema (e.g., /api/v1/swagger.json), YAML schema (e.g., /api/v1/swagger.yaml), and Swagger UI (e.g., /api/v1/docs/).

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.