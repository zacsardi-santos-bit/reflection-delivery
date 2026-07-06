I'm working on adding support for a server-wide default result storage configuration in Prefect.

*   The server must expose a GET endpoint at /admin/storage that returns HTTP 200 with a JSON body containing a single field 'default_result_storage_block_id'. When no default is configured, the value must be null.

*   The server must expose a PUT endpoint at /admin/storage that accepts a JSON body with a required 'default_result_storage_block_id' field (a UUID string). On success, the endpoint returns HTTP 200 with the updated configuration. If the field is missing, return HTTP 422. If the referenced block document does not exist, return HTTP 404 with body {"detail": "Block document <id> not found."}. If the block document exists but does not implement the storage interface, return HTTP 422 with body {"detail": "Block document <id> cannot be used for result storage."}.

*   The server must expose a DELETE endpoint at /admin/storage that clears the configured default result storage and returns HTTP 204 No Content.

*   A new schema class ServerDefaultResultStorage must exist at prefect.server.schemas.core with a single optional field default_result_storage_block_id (UUID or None, defaulting to None).

*   A new module prefect.server.models.storage_defaults must be created exposing three async functions: write_server_default_result_storage(session, storage_default) which persists the given ServerDefaultResultStorage; read_server_default_result_storage(session) which returns a ServerDefaultResultStorage (with default_result_storage_block_id=None when unset); and clear_server_default_result_storage(session) which removes the stored default and returns True on success.

*   The async PrefectClient must provide a read_server_default_result_storage() method that returns an object with a default_result_storage_block_id attribute (None when unset).

*   The async PrefectClient must provide an update_server_default_result_storage(block_document_id) method that accepts a UUID and returns the updated configuration object with default_result_storage_block_id set to the provided value.

*   The async PrefectClient must provide a clear_server_default_result_storage() method that clears the server-wide default result storage configuration.

*   The synchronous SyncPrefectClient must provide the same three methods — read_server_default_result_storage(), update_server_default_result_storage(block_document_id), and clear_server_default_result_storage() — with the same behavior and return shapes as their async counterparts.


*   Interface details: Type: Class
Name: ServerDefaultResultStorage
Location: src/prefect/server/schemas/core.py
Description: Schema representing the server-wide default result storage configuration. Has a single optional field `default_result_storage_block_id` (UUID or None, defaulting to None).

---

Type: Function
Name: write_server_default_result_storage
Location: src/prefect/server/models/storage_defaults.py
Signature: write_server_default_result_storage(session: AsyncSession, storage_default: ServerDefaultResultStorage) -> None
Description: Persists the given ServerDefaultResultStorage configuration to the database.

Type: Function
Name: read_server_default_result_storage
Location: src/prefect/server/models/storage_defaults.py
Signature: read_server_default_result_storage(session: AsyncSession) -> ServerDefaultResultStorage
Description: Reads and returns the current server default result storage configuration. Returns a ServerDefaultResultStorage with default_result_storage_block_id=None when no default has been configured.

Type: Function
Name: clear_server_default_result_storage
Location: src/prefect/server/models/storage_defaults.py
Signature: clear_server_default_result_storage(session: AsyncSession) -> bool
Description: Clears the stored server default result storage configuration. Returns True on success.

---

Type: Method
Name: read_server_default_result_storage
Location: src/prefect/client/orchestration/__init__.py (PrefectClient class, async)
Signature: read_server_default_result_storage() -> ServerDefaultResultStorage
Description: Calls GET /admin/storage and returns the current server default result storage configuration. The returned object has a default_result_storage_block_id attribute that is None when unset.

Type: Method
Name: update_server_default_result_storage
Location: src/prefect/client/orchestration/__init__.py (PrefectClient class, async)
Signature: update_server_default_result_storage(block_document_id: UUID) -> ServerDefaultResultStorage
Description: Calls PUT /admin/storage with the given block document UUID and returns the updated configuration.

Type: Method
Name: clear_server_default_result_storage
Location: src/prefect/client/orchestration/__init__.py (PrefectClient class, async)
Signature: clear_server_default_result_storage() -> None
Description: Calls DELETE /admin/storage to clear the server default result storage configuration.

---

Type: Method
Name: read_server_default_result_storage
Location: src/prefect/client/orchestration/__init__.py (SyncPrefectClient class, sync)
Signature: read_server_default_result_storage() -> ServerDefaultResultStorage
Description: Synchronous equivalent of the async client method. Returns the current server default result storage configuration.

Type: Method
Name: update_server_default_result_storage
Location: src/prefect/client/orchestration/__init__.py (SyncPrefectClient class, sync)
Signature: update_server_default_result_storage(block_document_id: UUID) -> ServerDefaultResultStorage
Description: Synchronous equivalent of the async client method. Updates and returns the server default result storage configuration.

Type: Method
Name: clear_server_default_result_storage
Location: src/prefect/client/orchestration/__init__.py (SyncPrefectClient class, sync)
Signature: clear_server_default_result_storage() -> None
Description: Synchronous equivalent of the async client method. Clears the server default result storage configuration.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.