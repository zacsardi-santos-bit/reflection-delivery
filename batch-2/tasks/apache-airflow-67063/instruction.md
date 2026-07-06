I'm working with the Airflow CLI tool and noticed that connection operations are not correctly sending the database schema field to the API server.

*   When creating a connection via the connections API client, the HTTP request body must serialize the schema field using its API alias ('schema'), not the internal Python attribute name ('schema_'). The key 'schema_' must not appear in the request body.

*   When performing a bulk connection operation via the connections API client, each connection entity in the HTTP request body must serialize the schema field using its API alias ('schema'), not the internal Python attribute name ('schema_'). The key 'schema_' must not appear in any entity.

*   When updating a connection via the connections API client, the HTTP request body must serialize the schema field using its API alias ('schema'), not the internal Python attribute name ('schema_'). The key 'schema_' must not appear in the request body.

*   When testing a connection via the connections API client, the HTTP request body must serialize the schema field using its API alias ('schema'), not the internal Python attribute name ('schema_'). The key 'schema_' must not appear in the request body.

*   When importing connections from a JSON file that includes a 'schema' key for a connection entry, the import function must preserve that schema value and include it in the bulk API request body as 'schema'. The key 'schema_' must not appear in the resulting entity in the request body.


*   Interface details: Type: Class
Name: ConnectionsOperations
Location: airflow-ctl/src/airflowctl/api/operations.py
Description: Handles all connection-related API operations. The following methods must serialize ConnectionBody using field aliases (so that 'schema_' is sent as 'schema' in request bodies):
  - create(connection: ConnectionBody) -> ConnectionResponse | ServerResponseError: POST to /api/v2/connections, must use by_alias=True when serializing
  - bulk(connections: BulkBodyConnectionBody) -> BulkResponse | ServerResponseError: PATCH to /api/v2/connections, must use by_alias=True when serializing
  - update(connection: ConnectionBody) -> ConnectionResponse | ServerResponseError: PATCH to /api/v2/connections/{connection_id}, must use by_alias=True when serializing
  - test(connection: ConnectionBody) -> ConnectionTestResponse | ServerResponseError: POST to /api/v2/connections/test, must use by_alias=True when serializing

Type: Function
Name: import_
Location: airflow-ctl/src/airflowctl/ctl/commands/connection_command.py
Signature: import_(args, api_client=NEW_API_CLIENT) -> None
Description: Imports connections from a JSON file and sends them to the API as a bulk operation. When the connection data in the JSON file contains a "schema" key, the function must pass it to the ConnectionBody constructor using the alias name "schema" (not "schema_"), so the field is correctly serialized and forwarded to the API.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.