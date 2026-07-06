I'm working on a hosting server for agents that need user approval before executing sensitive tool calls.

*   The _output_item_to_message and _item_to_message functions must be converted to async coroutines; all callers must await them.

*   InMemoryFunctionApprovalStorage must implement async save_approval_request(approval_request_id, request) and async load_approval_request(approval_request_id). Saving a duplicate ID raises ValueError with a message containing 'already exists'. Loading a missing ID raises KeyError.

*   FileBasedFunctionApprovalStorage must accept a string file path in its constructor, implement the same async save and load interface, create intermediate directories automatically, and persist data so a new instance at the same path can load previously saved entries. Loaded Content objects must preserve the embedded function call data including the function name. Saving a duplicate ID raises ValueError with a message containing 'already exists'. Loading a missing ID raises KeyError.

*   _output_item_to_message must accept an optional keyword argument 'approval_storage'. For mcp_approval_request output items, it must load the saved Content from storage and return a Message with role 'assistant' whose first content has type 'function_approval_request', with 'id' matching the request ID and 'function_call.name' matching the saved function name. For mcp_approval_response output items, it must load the original request from storage and return a Message with role 'user' whose first content has type 'function_approval_response', with the correct 'approved' boolean and the original 'function_call.name'. When approval_storage is None for either of these item types, a ValueError must be raised with a message matching 'ApprovalStorage is required'.

*   _item_to_message must accept an optional keyword argument 'approval_storage'. For mcp_approval_request input items with storage provided, it must return a Message with role 'assistant' and content type 'function_approval_request' with the 'id' field set. For mcp_approval_response input items with storage provided, it must return a Message with role 'user' and content type 'function_approval_response' with the correct 'approved' value.

*   ResponsesHostServer must have an attribute '_approval_storage' holding the active storage instance. When the agent response contains a function_approval_request content, the server must convert it to an mcp_approval_request output item (carrying the function 'name' and 'server_label') and persist the original Content in '_approval_storage' under the server-generated request ID. This must work for both streaming and non-streaming response modes.

*   In a subsequent turn, when an mcp_approval_response input item is received, the server must look up the original request in '_approval_storage' and deliver a function_approval_response content to the agent with the correct 'approved' value and the original 'function_call.name'. When the referenced approval_request_id is not found in storage, the server must return an HTTP status code of 500 or higher.


*   Interface details: Type: Class
Name: InMemoryFunctionApprovalStorage
Location: python/packages/foundry_hosting/agent_framework_foundry_hosting/_responses.py
Description: In-memory storage for function approval requests. Stores Content objects keyed by approval request ID. Data is ephemeral and not shared across instances.
Signature:
  __init__(self) -> None
  async save_approval_request(self, approval_request_id: str, request: Content) -> None
  async load_approval_request(self, approval_request_id: str) -> Content

Type: Class
Name: FileBasedFunctionApprovalStorage
Location: python/packages/foundry_hosting/agent_framework_foundry_hosting/_responses.py
Description: File-based persistent storage for function approval requests. Persists Content objects to a JSON file at the given path. Creates parent directories as needed. Data survives across instances pointing at the same path.
Signature:
  __init__(self, storage_path: str) -> None
  async save_approval_request(self, approval_request_id: str, request: Content) -> None
  async load_approval_request(self, approval_request_id: str) -> Content

Type: Function
Name: _output_item_to_message
Location: python/packages/foundry_hosting/agent_framework_foundry_hosting/_responses.py
Signature: async def _output_item_to_message(item: OutputItem, *, approval_storage=None) -> Message
Description: Converts an OutputItem to a Message. Must be an async coroutine. Accepts an optional keyword-only argument 'approval_storage' used when processing MCP approval request and response items.

Type: Function
Name: _item_to_message
Location: python/packages/foundry_hosting/agent_framework_foundry_hosting/_responses.py
Signature: async def _item_to_message(item: Item, *, approval_storage=None) -> Message
Description: Converts an input Item to a Message. Must be an async coroutine. Accepts an optional keyword-only argument 'approval_storage' used when processing MCP approval request and response items.

Type: Attribute
Name: _approval_storage
Location: python/packages/foundry_hosting/agent_framework_foundry_hosting/_responses.py (on ResponsesHostServer class)
Description: Attribute on ResponsesHostServer holding the active approval storage instance (either InMemoryFunctionApprovalStorage or FileBasedFunctionApprovalStorage). Tests access this attribute directly via server._approval_storage to verify storage state after a round-trip.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.