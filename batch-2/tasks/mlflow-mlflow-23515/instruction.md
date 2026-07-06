I'm working on MLflow and need to implement a registry for managing remote AI tool servers in the database-backed tracking store.

*   The store must implement create_mcp_server(name, description=None, icons=None) returning an MCPServer entity with name, description, creation_timestamp, and icons fields. Raises MlflowException with error_code 'RESOURCE_ALREADY_EXISTS' (message matching 'already exists') if the server already exists, and error_code 'INVALID_PARAMETER_VALUE' (message matching 'must not be empty') if name is empty.

*   The store must implement get_mcp_server(name) returning an MCPServer entity with name, description, tags (dict), status, aliases (dict), access_bindings (list), and latest_version fields. Raises MlflowException with error_code 'RESOURCE_DOES_NOT_EXIST' (message matching 'not found') if the server does not exist. Access bindings returned by get_mcp_server must include a resolved_version field.

*   The store must implement search_mcp_servers(max_results=None, page_token=None, filter_string=None, order_by=None) returning a paged list with a token attribute. Supports pagination, default ordering by name ASC, and order_by with values like ['name DESC'] or ['name ASC']. Raises MlflowException (message 'Invalid order_by key') for invalid order keys and (message 'Duplicate order_by') for duplicate keys.

*   search_mcp_servers must support a filter_string with: exact name match (name = 'x'), LIKE match (name LIKE '%x%'), tag filtering (tags.key = 'value'), combined AND filters, has_access_bindings = 'true'/'false', status = 'active'/'deprecated'/'draft', status IN ('active', 'deprecated'), numeric timestamp filter (created_at > 0), and empty string (returns all). Raises MlflowException (message 'Invalid attribute key') for unknown filter attributes. The has_access_bindings filter must exclude bindings pointing to deleted versions. Status filtering must use the resolved latest version status.

*   The store must implement update_mcp_server(name, description=None, display_name=None, latest_version=None) returning a complete MCPServer entity including tags, status, and aliases. Raises MlflowException (message matching 'not found') if server does not exist or if latest_version does not exist. Raises MlflowException (message matching 'Cannot pin') if latest_version points to a DRAFT version.

*   The store must implement delete_mcp_server(name) which cascades to delete all associated versions, tags, aliases, and access bindings. Raises MlflowException (message matching 'not found') if server does not exist.

*   The store must implement create_mcp_server_version(server_json, source=None, status=None, tools=None) returning an MCPServerVersion entity with name, version, status, source, server_json, and tools fields. The server_json dict must contain both 'name' and 'version' keys; raises MlflowException with error_code 'INVALID_PARAMETER_VALUE' (message matching 'name.*version') otherwise. If the parent server does not exist, it is automatically created without overwriting an existing server's description. Raises MlflowException with error_code 'RESOURCE_ALREADY_EXISTS' if the version already exists, including soft-deleted versions.

*   create_mcp_server_version must preserve tools=[]: an empty list is stored as an empty list. tools=None stores None. Supports MCPTool objects with name and description fields.

*   The store must implement get_mcp_server_version(server_name, version) returning an MCPServerVersion entity with tags. Raises MlflowException (message matching 'not found') if not found or soft-deleted.

*   The store must implement get_latest_mcp_server_version(server_name) returning the latest eligible MCPServerVersion. If the server has a pinned latest_version, return that version. Otherwise fall back to the most recently created non-DRAFT version; when timestamps are equal, break ties by version string DESC. Raises MlflowException (message matching 'No eligible') if no eligible version exists. Raises MlflowException (message matching 'not found') if server does not exist.

*   The store must implement get_mcp_server_version_by_alias(server_name, alias) returning the MCPServerVersion the alias points to. The alias 'latest' is reserved and resolves via get_latest_mcp_server_version logic. Raises MlflowException (message matching 'not found') if alias does not exist.

*   The store must implement search_mcp_server_versions(server_name, max_results=None, page_token=None, filter_string=None, order_by=None) scoped to the given server, with pagination, filter_string (status = 'active', status IN ('active', 'deprecated')), and order_by (backtick-quoted fields like ['`version` DESC']). Deleted versions must be excluded.

*   The store must implement update_mcp_server_version(server_name, version, status=None, display_name=None, tools=None) returning the complete MCPServerVersion entity with tags, status, and server_json. Valid status transitions: DRAFT→ACTIVE, DRAFT→DELETED, ACTIVE→DRAFT, ACTIVE→DEPRECATED, DEPRECATED→ACTIVE, DEPRECATED→DELETED. Invalid transitions (ACTIVE→DELETED directly, DRAFT→DEPRECATED) raise MlflowException with error_code 'INVALID_PARAMETER_VALUE' (message 'Invalid status transition'). Updating a pinned version to DRAFT clears the parent server's latest_version pin. Raises MlflowException with error_code 'RESOURCE_DOES_NOT_EXIST' (message matching 'not found') if version is deleted or missing. tools=[] is preserved; tools=None clears to None.

*   The store must implement delete_mcp_server_version(server_name, version) as a soft-delete that makes the version invisible to get and search. Cannot delete an ACTIVE version directly; raises MlflowException (message 'Invalid status transition'). Deleting a version cascades: clears aliases pointing to it, deletes access bindings referencing it (directly or via alias), and clears the parent server's latest_version pin if it pointed to this version. Raises MlflowException (message matching 'not found') if version does not exist.

*   The store must implement create_mcp_access_binding(server_name, endpoint_url, server_version=None, server_alias=None, transport_type=MCPRemoteTransportType.STREAMABLE_HTTP) returning an MCPAccessBinding entity with server_name, endpoint_url, server_version, server_alias, transport_type, binding_id, and resolved_version fields. Exactly one of server_version or server_alias must be provided; raises MlflowException (message 'Exactly one') otherwise. Raises MlflowException (message matching 'not found') if server, version, or alias does not exist. Raises MlflowException (message matching 'deleted MCP server version') if referenced version is deleted. Default transport_type is MCPRemoteTransportType.STREAMABLE_HTTP.

*   The store must implement get_mcp_access_binding(server_name, binding_id) returning an MCPAccessBinding with resolved_version populated. Raises MlflowException (message matching 'not found') if binding does not exist.

*   The store must implement search_mcp_access_bindings(server_name=None, server_version=None, server_alias=None, max_results=None, page_token=None, filter_string=None) returning a paged list. Supports filtering by server_name, server_version (direct bindings only), server_alias (alias bindings only), and filter_string (transport_type = 'streamable-http', status = 'active'). Bindings referencing deleted versions must be excluded. Each returned binding must have resolved_version populated.

*   The store must implement update_mcp_access_binding(server_name, binding_id, endpoint_url=..., server_version=None, server_alias=None, transport_type=None) returning the updated MCPAccessBinding with resolved_version. Setting server_version clears server_alias; setting server_alias clears server_version. Raises MlflowException with error_code 'INVALID_PARAMETER_VALUE' (message 'endpoint_url cannot be None') if endpoint_url is None. Raises MlflowException (message 'Cannot set both') if both server_version and server_alias are specified. Raises MlflowException (message matching 'not found') or (message matching 'deleted MCP server version') for invalid version/alias. Raises MlflowException (message 'does not belong') if binding does not belong to the given server.

*   The store must implement delete_mcp_access_binding(server_name, binding_id). Raises MlflowException (message 'does not belong') if binding does not belong to the given server.

*   The store must implement set_mcp_server_tag(server_name, key, value) as an upsert. delete_mcp_server_tag(server_name, key) raises MlflowException (message matching 'not found') if tag does not exist.

*   The store must implement set_mcp_server_version_tag(server_name, version, key, value) and delete_mcp_server_version_tag(server_name, version, key). Both raise MlflowException with error_code 'RESOURCE_DOES_NOT_EXIST' (message matching 'not found') if the version is deleted or missing. delete_mcp_server_version_tag also raises (message matching 'not found') if the tag does not exist.

*   The store must implement set_mcp_server_alias(server_name, alias, version) as an upsert. The alias name 'latest' is reserved; raises MlflowException with error_code 'INVALID_PARAMETER_VALUE' (message 'reserved'). Raises MlflowException (message matching 'not found') if version does not exist. Raises MlflowException (message matching 'Cannot set alias') if version is deleted. delete_mcp_server_alias(server_name, alias) removes the alias and cascades to delete all access bindings that reference it. Raises MlflowException (message matching 'not found') if alias does not exist.

*   MCPStatus, MCPTool, and MCPRemoteTransportType must be importable from mlflow.entities.mcp_server. MCPStatus has values: DRAFT, ACTIVE, DEPRECATED, DELETED. MCPRemoteTransportType has values: STREAMABLE_HTTP (serialized as 'streamable-http'), SSE (serialized as 'sse'). MCPTool has at least name and description fields.

*   The SQLAlchemy DB model classes SqlMCPServer, SqlMCPServerVersion, SqlMCPServerTag, SqlMCPServerVersionTag, SqlMCPServerAlias, and SqlMCPAccessBinding must be importable from mlflow.store.tracking.dbmodels.models.

*   The get_current_time_millis function used internally must be patchable at the path mlflow.store.tracking.mcp_server_registry.sqlalchemy_mixin.get_current_time_millis to allow tiebreaker tests to work correctly.


*   Interface details: ## Entity Classes

Type: Class
Name: MCPStatus
Location: mlflow/entities/mcp_server.py
Description: Enum-like class representing lifecycle states for an MCP server version. Values: DRAFT, ACTIVE, DEPRECATED, DELETED.

Type: Class
Name: MCPTool
Location: mlflow/entities/mcp_server.py
Description: Represents a tool exposed by an MCP server. Has at minimum a `name` field and an optional `description` field. Instantiated as MCPTool(name="...", description="...").

Type: Class
Name: MCPRemoteTransportType
Location: mlflow/entities/mcp_server.py
Description: Enum-like class for MCP transport types. Values: STREAMABLE_HTTP (serialized as the string 'streamable-http') and SSE (serialized as the string 'sse'). STREAMABLE_HTTP is the default transport type for access bindings.

## Database Model Classes

Type: Class
Name: SqlMCPServer
Location: mlflow/store/tracking/dbmodels/models.py
Description: SQLAlchemy model representing an MCP server entry.

Type: Class
Name: SqlMCPServerVersion
Location: mlflow/store/tracking/dbmodels/models.py
Description: SQLAlchemy model representing a versioned snapshot of an MCP server.

Type: Class
Name: SqlMCPServerTag
Location: mlflow/store/tracking/dbmodels/models.py
Description: SQLAlchemy model representing a tag on an MCP server.

Type: Class
Name: SqlMCPServerVersionTag
Location: mlflow/store/tracking/dbmodels/models.py
Description: SQLAlchemy model representing a tag on an MCP server version.

Type: Class
Name: SqlMCPServerAlias
Location: mlflow/store/tracking/dbmodels/models.py
Description: SQLAlchemy model representing a named alias pointing to a specific MCP server version.

Type: Class
Name: SqlMCPAccessBinding
Location: mlflow/store/tracking/dbmodels/models.py
Description: SQLAlchemy model representing an access binding associating an endpoint URL with an MCP server version or alias.

## Store Methods

All methods below must be implemented on the existing SQLAlchemy tracking store class (or a mixin at mlflow/store/tracking/mcp_server_registry/sqlalchemy_mixin.py).

Type: Function
Name: create_mcp_server
Location: mlflow/store/tracking/sqlalchemy_store.py (or mcp_server_registry mixin)
Signature: create_mcp_server(name: str, description: str = None, icons: list = None) -> MCPServer
Description: Creates a new MCP server record. Raises MlflowException (RESOURCE_ALREADY_EXISTS, message matching "already exists") on duplicate name. Raises MlflowException (INVALID_PARAMETER_VALUE, message matching "must not be empty") for empty name.

Type: Function
Name: get_mcp_server
Location: mlflow/store/tracking/sqlalchemy_store.py (or mcp_server_registry mixin)
Signature: get_mcp_server(name: str) -> MCPServer
Description: Retrieves a server entity with tags, aliases, access_bindings (each with resolved_version), and resolved status. Raises MlflowException (RESOURCE_DOES_NOT_EXIST, message matching "not found") if missing.

Type: Function
Name: search_mcp_servers
Location: mlflow/store/tracking/sqlalchemy_store.py (or mcp_server_registry mixin)
Signature: search_mcp_servers(max_results: int = None, page_token: str = None, filter_string: str = None, order_by: list = None) -> PagedList[MCPServer]
Description: Returns a paged list of MCPServer objects. Default order is name ASC. Supports filter_string and order_by. Returns a list object with a .token attribute.

Type: Function
Name: update_mcp_server
Location: mlflow/store/tracking/sqlalchemy_store.py (or mcp_server_registry mixin)
Signature: update_mcp_server(name: str, description: str = None, display_name: str = None, latest_version: str = None) -> MCPServer
Description: Updates server metadata. Returns complete entity. Raises MlflowException if not found, if latest_version does not exist (message "not found"), or if latest_version is DRAFT (message matching "Cannot pin").

Type: Function
Name: delete_mcp_server
Location: mlflow/store/tracking/sqlalchemy_store.py (or mcp_server_registry mixin)
Signature: delete_mcp_server(name: str) -> None
Description: Deletes a server and cascades to all versions, tags, aliases, and access bindings. Raises MlflowException (message matching "not found") if missing.

Type: Function
Name: create_mcp_server_version
Location: mlflow/store/tracking/sqlalchemy_store.py (or mcp_server_registry mixin)
Signature: create_mcp_server_version(server_json: dict, source: str = None, status: MCPStatus = None, tools: list = None) -> MCPServerVersion
Description: Creates a versioned snapshot. server_json must contain both "name" and "version" keys; raises MlflowException (INVALID_PARAMETER_VALUE, message matching "name.*version") otherwise. Auto-creates parent server. Raises MlflowException (RESOURCE_ALREADY_EXISTS, message matching "already exists") for duplicate (including soft-deleted) versions.

Type: Function
Name: get_mcp_server_version
Location: mlflow/store/tracking/sqlalchemy_store.py (or mcp_server_registry mixin)
Signature: get_mcp_server_version(server_name: str, version: str) -> MCPServerVersion
Description: Returns version entity with tags. Raises MlflowException (message matching "not found") if soft-deleted or missing.

Type: Function
Name: get_latest_mcp_server_version
Location: mlflow/store/tracking/sqlalchemy_store.py (or mcp_server_registry mixin)
Signature: get_latest_mcp_server_version(server_name: str) -> MCPServerVersion
Description: Returns the pinned latest version if set, otherwise falls back to most recently created non-DRAFT version. Tiebreaker: version string DESC. Raises MlflowException (message matching "No eligible") if no eligible version. Raises MlflowException (message matching "not found") if server missing.

Type: Function
Name: get_mcp_server_version_by_alias
Location: mlflow/store/tracking/sqlalchemy_store.py (or mcp_server_registry mixin)
Signature: get_mcp_server_version_by_alias(server_name: str, alias: str) -> MCPServerVersion
Description: Resolves alias to version. The alias "latest" is reserved and resolves via get_latest_mcp_server_version logic. Raises MlflowException (message matching "not found") if alias missing.

Type: Function
Name: search_mcp_server_versions
Location: mlflow/store/tracking/sqlalchemy_store.py (or mcp_server_registry mixin)
Signature: search_mcp_server_versions(server_name: str, max_results: int = None, page_token: str = None, filter_string: str = None, order_by: list = None) -> PagedList[MCPServerVersion]
Description: Scoped search for versions. Supports pagination, filter_string (status = 'active', status IN ('active','deprecated')), and order_by with backtick-quoted fields. Excludes deleted versions.

Type: Function
Name: update_mcp_server_version
Location: mlflow/store/tracking/sqlalchemy_store.py (or mcp_server_registry mixin)
Signature: update_mcp_server_version(server_name: str, version: str, status: MCPStatus = None, display_name: str = None, tools: list = None) -> MCPServerVersion
Description: Updates a version. Valid transitions: DRAFT→ACTIVE, DRAFT→DELETED, ACTIVE→DRAFT, ACTIVE→DEPRECATED, DEPRECATED→ACTIVE, DEPRECATED→DELETED. Invalid transitions raise MlflowException (INVALID_PARAMETER_VALUE, message "Invalid status transition"). Updating to DRAFT clears parent server's latest_version pin. Raises MlflowException (RESOURCE_DOES_NOT_EXIST, message "not found") if deleted or missing.

Type: Function
Name: delete_mcp_server_version
Location: mlflow/store/tracking/sqlalchemy_store.py (or mcp_server_registry mixin)
Signature: delete_mcp_server_version(server_name: str, version: str) -> None
Description: Soft-deletes version. Raises MlflowException (message "Invalid status transition") if version is ACTIVE. Cascades: removes aliases pointing to this version, deletes associated access bindings (direct and via alias), clears parent server's latest_version pin. Raises MlflowException (message "not found") if missing.

Type: Function
Name: create_mcp_access_binding
Location: mlflow/store/tracking/sqlalchemy_store.py (or mcp_server_registry mixin)
Signature: create_mcp_access_binding(server_name: str, endpoint_url: str, server_version: str = None, server_alias: str = None, transport_type: MCPRemoteTransportType = MCPRemoteTransportType.STREAMABLE_HTTP) -> MCPAccessBinding
Description: Creates an access binding. Exactly one of server_version or server_alias required (raises MlflowException message "Exactly one" otherwise). Raises MlflowException (message "not found") if server/version/alias missing. Raises MlflowException (message "deleted MCP server version") if version is soft-deleted. Returns binding with resolved_version populated.

Type: Function
Name: get_mcp_access_binding
Location: mlflow/store/tracking/sqlalchemy_store.py (or mcp_server_registry mixin)
Signature: get_mcp_access_binding(server_name: str, binding_id: int) -> MCPAccessBinding
Description: Returns binding with resolved_version populated. Raises MlflowException (message "not found") if missing.

Type: Function
Name: search_mcp_access_bindings
Location: mlflow/store/tracking/sqlalchemy_store.py (or mcp_server_registry mixin)
Signature: search_mcp_access_bindings(server_name: str = None, server_version: str = None, server_alias: str = None, max_results: int = None, page_token: str = None, filter_string: str = None) -> PagedList[MCPAccessBinding]
Description: Searches access bindings. server_version filter returns only direct-version bindings; server_alias filter returns only alias bindings. Excludes bindings to deleted versions. Supports filter_string (transport_type = 'streamable-http', status = 'active') and pagination. Each result includes resolved_version.

Type: Function
Name: update_mcp_access_binding
Location: mlflow/store/tracking/sqlalchemy_store.py (or mcp_server_registry mixin)
Signature: update_mcp_access_binding(server_name: str, binding_id: int, endpoint_url: str = ..., server_version: str = None, server_alias: str = None, transport_type: MCPRemoteTransportType = None) -> MCPAccessBinding
Description: Updates a binding. Setting server_version clears server_alias and vice versa. Raises MlflowException (INVALID_PARAMETER_VALUE, message "endpoint_url cannot be None") if endpoint_url=None. Raises MlflowException (message "Cannot set both") if both version and alias provided. Raises MlflowException (message "does not belong") if binding belongs to a different server. Raises MlflowException for invalid/deleted version or alias. Returns updated binding with resolved_version.

Type: Function
Name: delete_mcp_access_binding
Location: mlflow/store/tracking/sqlalchemy_store.py (or mcp_server_registry mixin)
Signature: delete_mcp_access_binding(server_name: str, binding_id: int) -> None
Description: Deletes a binding. Raises MlflowException (message "does not belong") if binding belongs to a different server.

Type: Function
Name: set_mcp_server_tag
Location: mlflow/store/tracking/sqlalchemy_store.py (or mcp_server_registry mixin)
Signature: set_mcp_server_tag(server_name: str, key: str, value: str) -> None
Description: Upserts a tag on a server.

Type: Function
Name: delete_mcp_server_tag
Location: mlflow/store/tracking/sqlalchemy_store.py (or mcp_server_registry mixin)
Signature: delete_mcp_server_tag(server_name: str, key: str) -> None
Description: Deletes a tag. Raises MlflowException (message "not found") if tag does not exist.

Type: Function
Name: set_mcp_server_version_tag
Location: mlflow/store/tracking/sqlalchemy_store.py (or mcp_server_registry mixin)
Signature: set_mcp_server_version_tag(server_name: str, version: str, key: str, value: str) -> None
Description: Upserts a tag on a version. Raises MlflowException (RESOURCE_DOES_NOT_EXIST, message "not found") if version is deleted or missing.

Type: Function
Name: delete_mcp_server_version_tag
Location: mlflow/store/tracking/sqlalchemy_store.py (or mcp_server_registry mixin)
Signature: delete_mcp_server_version_tag(server_name: str, version: str, key: str) -> None
Description: Deletes a tag on a version. Raises MlflowException (RESOURCE_DOES_NOT_EXIST, message "not found") if version is deleted. Raises MlflowException (message "not found") if tag does not exist.

Type: Function
Name: set_mcp_server_alias
Location: mlflow/store/tracking/sqlalchemy_store.py (or mcp_server_registry mixin)
Signature: set_mcp_server_alias(server_name: str, alias: str, version: str) -> None
Description: Upserts an alias. The alias "latest" is reserved; raises MlflowException (INVALID_PARAMETER_VALUE, message "reserved"). Raises MlflowException (message "not found") if version missing. Raises MlflowException (message "Cannot set alias") if version is soft-deleted.

Type: Function
Name: delete_mcp_server_alias
Location: mlflow/store/tracking/sqlalchemy_store.py (or mcp_server_registry mixin)
Signature: delete_mcp_server_alias(server_name: str, alias: str) -> None
Description: Deletes an alias and cascades to delete all access bindings that reference it. Raises MlflowException (message "not found") if alias does not exist.

## Patchable Utility

The internal timestamp function must be importable and patchable at:
  mlflow.store.tracking.mcp_server_registry.sqlalchemy_mixin.get_current_time_millis


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.