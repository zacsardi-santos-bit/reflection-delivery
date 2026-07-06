## Description

Currently, the admin controls for the CLI allow administrators to define an allowlist of permitted MCP servers, which filters out any user-configured servers not on that list. However, there is no corresponding mechanism for admins to *mandate* that specific MCP servers are always present in the configuration — regardless of what individual users have set up locally.

This gap means compliance-critical or infrastructure servers can be absent from a user's effective configuration because the user simply hasn't added them, or because the user has a conflicting local entry with the same name.

## Expected Behavior

- Administrators should be able to define a set of required MCP servers that are automatically injected into every user's effective configuration.
- Required servers should take full precedence over any locally configured server with the same name, completely replacing local configuration entries.
- Required servers should be injected with a trusted status by default.
- Auth configuration (such as credential provider type, OAuth scopes, target audience, and custom headers) defined on required servers should be preserved as-is.
- Tool allow/deny filters on required servers should be preserved as-is.
- Required server names should be tracked separately so the system knows which servers were admin-mandated.
- When admin settings include required server definitions, those definitions should flow through the settings pipeline and be reflected in the effective merged configuration.
- The JSON configuration format used to define admin MCP settings should support a dedicated field for required servers, separate from the existing allowed-servers field.

## Why This Matters

Without a "required servers" mechanism, administrators cannot ensure that critical tooling is always available in every user session. This feature closes that gap, enabling organizations to enforce the presence of compliance or infrastructure servers in all CLI instances.
