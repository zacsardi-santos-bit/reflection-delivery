## Description

The OData service inquirer currently only supports connecting to a service via a direct URL. There is no way for users to connect to a full SAP backend system and browse the catalog of available services. We need to add support for on-premises ABAP SAP system connections as a new data source option.

Additionally, the internal connection validation API uses positional boolean parameters that make call sites hard to read and extend. These should be replaced with a structured options object.

## Expected Behavior

- Users can select an on-premises ABAP SAP system as a data source type
- The tool prompts for a system URL, an optional SAP client number, and credentials (username/password) when the system requires authentication
- Once connected, users can browse and select from available OData services registered in the system catalog
- The tool suggests a default name for saving the system connection, automatically generating a non-conflicting name when the system URL already exists in storage
- When no services are found, or when the selected service type is not intended for UI consumption, appropriate warning messages are shown
- Service selection supports type-ahead autocomplete search when enabled
- The connection validator's method for validating a URL accepts an options object (with fields for certificate error handling, force re-validation, system mode, and OData version) instead of positional boolean arguments
- The connection validator's authentication method accepts an options object (with fields for system mode, SAP client, and certificate error handling) instead of positional arguments
- When connecting to a SAP system (as opposed to a service URL), the validator uses the lower-level provider API, providing the base host URL separately from the service path
- When a URL returns a "not found" response during authentication validation, the validator returns a descriptive error string rather than a plain false value

## Why This Matters

Developers using this tool to scaffold SAP Fiori applications need to be able to connect to a backend ABAP system and select a service from its catalog, not just hardcode a direct service URL. Supporting this flow removes a significant barrier for users who manage their SAP services through a system catalog rather than knowing the full service URL ahead of time.
