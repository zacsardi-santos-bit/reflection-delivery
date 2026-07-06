I'm working on an attack paths feature for a cloud security platform that stores graph data in a graph database.

*   The normalize_query_payload function (renamed from normalize_run_payload) must accept a payload and return raw_data unchanged if it is not a dict, otherwise extract the attributes from the data.attributes section.

*   The prepare_parameters function (renamed from prepare_query_parameters) must accept an AttackPathsQueryDefinition, a dict of provided parameters, a provider_uid string, and a provider_id string, and must raise ValidationError for missing required parameters, unknown parameters, or invalid cast values.

*   The execute_query function (renamed from execute_attack_paths_query) must return a dict containing 'nodes', 'relationships', 'total_nodes' (int), and 'truncated' (bool, always False for predefined queries). It must raise PermissionDenied when a read-only violation occurs and APIException (with one logger.error call) on database errors.

*   The normalize_custom_query_payload function must accept a payload: return it unchanged if not a dict; return it unchanged if it is a flat dict without a nested 'data' dict; extract {'cypher': value} from data.attributes.cypher if the payload has a nested data.attributes structure.

*   The execute_custom_query function must accept database_name (str), cypher (str), and provider_id (str). It must call graph_database.execute_read_query with keyword arguments database and cypher. It must return a dict with 'nodes', 'relationships', 'total_nodes', and 'truncated'. On WriteQueryNotAllowedException it must raise PermissionDenied. On GraphDatabaseQueryException it must log the error exactly once and raise APIException.

*   The _truncate_graph function must accept a graph dict with keys 'nodes', 'relationships', 'total_nodes', and 'truncated'. When the number of nodes exceeds graph_database.MAX_CUSTOM_QUERY_NODES, it must slice nodes to the first MAX_CUSTOM_QUERY_NODES entries, remove any relationships whose 'source' or 'target' node IDs are not in the retained nodes, set 'truncated' to True, and preserve the original 'total_nodes' value. When no truncation is needed, the graph is returned unchanged with 'truncated' remaining False.

*   The get_cartography_schema function must accept database_name (str) and provider_id (str). It must return None when no cartography metadata record is found. When a record is found, it must return a dict with: 'id' set to '{provider}-{version}', 'provider' extracted as the part after ':' in the module_name (e.g. 'aws', 'azure', 'gcp'), 'cartography_version' set to the version string, 'schema_url' set to a GitHub URL containing both the version and the provider path segment ('/aws/', '/azure/', or '/gcp/'), and 'raw_schema_url' set to a raw.githubusercontent.com URL containing both the version and the provider path segment. On GraphDatabaseQueryException it must log the error exactly once and raise APIException.

*   The POST endpoint at the route named 'attack-paths-scans-queries-custom' must return 200 with nodes, relationships, total_nodes, and truncated in data.attributes when the query returns results; 404 when the nodes list is empty; 400 with an error detail containing 'not available' when graph_data_ready is False; and 403 when execute_custom_query raises PermissionDenied.

*   The GET endpoint at the route named 'attack-paths-scans-schema' must call get_cartography_schema with the database name and provider_id string, return 200 with provider, cartography_version, schema_url, and raw_schema_url in data.attributes when schema is found, return 404 with a response body containing 'No cartography schema metadata' when schema is None, and return 400 when graph_data_ready is False.

*   The MAX_CUSTOM_QUERY_NODES constant must be defined in the api/src/backend/api/attack_paths/database.py module (accessible as graph_database.MAX_CUSTOM_QUERY_NODES) and must be configurable via the ATTACK_PATHS_MAX_CUSTOM_QUERY_NODES environment variable with a default of 250.


*   Interface details: Type: Function
Name: normalize_query_payload
Location: api/src/backend/api/attack_paths/views_helpers.py
Signature: normalize_query_payload(raw_data) -> Any
Description: Renamed from normalize_run_payload. Extracts the attributes section from a nested JSON:API-style payload dict. If raw_data is not a dict, returns it unchanged. If the dict has a "data" key containing a dict with an "attributes" key, returns the attributes dict. Otherwise returns raw_data unchanged.

Type: Function
Name: prepare_parameters
Location: api/src/backend/api/attack_paths/views_helpers.py
Signature: prepare_parameters(definition: AttackPathsQueryDefinition, provided_parameters: dict, provider_uid: str, provider_id: str) -> dict
Description: Renamed from prepare_query_parameters. Validates and prepares query parameters for an attack paths query definition.

Type: Function
Name: execute_query
Location: api/src/backend/api/attack_paths/views_helpers.py
Signature: execute_query(database_name: str, definition: AttackPathsQueryDefinition, parameters: dict, provider_id: str) -> dict
Description: Renamed from execute_attack_paths_query. Executes a predefined attack paths query. The returned dict now includes "total_nodes" (int) and "truncated" (bool, always False) fields in addition to "nodes" and "relationships".

Type: Function
Name: normalize_custom_query_payload
Location: api/src/backend/api/attack_paths/views_helpers.py
Signature: normalize_custom_query_payload(raw_data) -> Any
Description: Extracts the cypher field from a nested JSON:API-style payload. If raw_data is not a dict, returns it unchanged. If the dict has a "data" key containing a dict, extracts attributes.cypher and returns {"cypher": value}. If the dict is already flat (no nested "data" dict), returns raw_data unchanged.

Type: Function
Name: execute_custom_query
Location: api/src/backend/api/attack_paths/views_helpers.py
Signature: execute_custom_query(database_name: str, cypher: str, provider_id: str) -> dict
Description: Executes an arbitrary read-only Cypher query against the graph database. Returns a dict with keys "nodes", "relationships", "total_nodes" (int), and "truncated" (bool). Raises PermissionDenied if a write query is attempted (WriteQueryNotAllowedException). Logs and raises APIException on GraphDatabaseQueryException.

Type: Function
Name: _truncate_graph
Location: api/src/backend/api/attack_paths/views_helpers.py
Signature: _truncate_graph(graph: dict) -> dict
Description: Truncates a serialized graph dict if total_nodes exceeds graph_database.MAX_CUSTOM_QUERY_NODES. When truncation occurs: slices nodes to the first MAX_CUSTOM_QUERY_NODES entries, removes relationships whose source or target node IDs are not in the retained set, sets "truncated" to True, and preserves the original "total_nodes" count. When no truncation is needed, returns the graph unchanged with "truncated" as False.

Type: Function
Name: get_cartography_schema
Location: api/src/backend/api/attack_paths/views_helpers.py
Signature: get_cartography_schema(database_name: str, provider_id: str) -> dict | None
Description: Queries the graph database for cartography schema metadata. Returns a dict with keys: "id" (str, format "{provider}-{version}"), "provider" (str, extracted from module_name after the colon, e.g. "aws", "azure", "gcp"), "cartography_version" (str), "schema_url" (str, GitHub URL containing version and provider path), "raw_schema_url" (str, raw.githubusercontent.com URL containing version and provider path). Returns None if no record is found. Logs and raises APIException on GraphDatabaseQueryException.

Type: Constant
Name: MAX_CUSTOM_QUERY_NODES
Location: api/src/backend/api/attack_paths/database.py
Description: Integer constant controlling the maximum number of nodes returned by custom queries. Must be accessible as graph_database.MAX_CUSTOM_QUERY_NODES. Read from environment variable ATTACK_PATHS_MAX_CUSTOM_QUERY_NODES, defaulting to 250.

Type: URL Route
Name: attack-paths-scans-queries-custom
Location: api/src/backend/api/v1/views.py
Description: POST endpoint at url_path="queries/custom", url_name="queries-custom" on the AttackPathsScanViewSet (detail=True). Handles run_custom_attack_paths_query action. Returns 200 with graph data, 404 when nodes list is empty, 400 with "not available" in error detail when graph_data_ready is False, 403 when a write query is submitted.

Type: URL Route
Name: attack-paths-scans-schema
Location: api/src/backend/api/v1/views.py
Description: GET endpoint at url_path="schema", url_name="schema" on the AttackPathsScanViewSet (detail=True). Handles cartography_schema action. Returns 200 with schema data (provider, cartography_version, schema_url, raw_schema_url in data.attributes), 404 with "No cartography schema metadata" in response body when schema is None, 400 when graph_data_ready is False.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.