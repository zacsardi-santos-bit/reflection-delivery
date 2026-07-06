I'm working on adding support for Model Context Protocol (MCP) servers in Airflow so that AI agent tasks can use externally-hosted tools.

*   MCPHook must accept an optional mcp_conn_id parameter (defaulting to 'mcp_default') and an optional tool_prefix parameter, storing both as instance attributes.

*   MCPHook.get_conn() must read the Airflow connection identified by mcp_conn_id and return an MCP server object based on the transport type. The default transport (when no 'transport' key is present in connection extras) is streamable HTTP.

*   For HTTP and SSE transports, get_conn() must use the connection's host field as the server URL and raise a ValueError with a message matching 'requires a host URL' when no host is set.

*   For HTTP transport, if the connection's password field is set, get_conn() must pass headers={'Authorization': 'Bearer <password>'} to the server constructor; otherwise pass headers=None.

*   For SSE transport (extra JSON with transport='sse'), get_conn() must create a pydantic_ai.mcp.MCPServerSSE instance with the same URL/headers/tool_prefix arguments as HTTP.

*   For stdio transport (extra JSON with transport='stdio'), get_conn() must create a pydantic_ai.mcp.MCPServerStdio instance passing command (from extra), args (list, default empty or provided), and timeout (from extra, default 10). If args is a bare string in the extras, it must be converted to a single-element list.

*   For stdio transport, if no 'command' key is present in the connection extras, get_conn() must raise a ValueError with a message matching 'requires \'command\''.

*   If the transport value is not one of 'http', 'sse', or 'stdio', get_conn() must raise a ValueError with a message matching 'Unknown transport'.

*   get_conn() must cache its result: calling it multiple times on the same hook instance must return the identical server object and only construct the server once.

*   The tool_prefix value from the hook must be forwarded as the tool_prefix keyword argument to whichever MCP server constructor is called.

*   MCPHook.test_connection() must return (True, message) where message contains the word 'valid' (case-insensitive) when the connection configuration is valid, and (False, message) where message contains 'host URL' when the configuration is invalid (e.g., no host for HTTP transport).

*   MCPHook.get_ui_field_behaviour() must return a dict containing a 'hidden_fields' key whose value includes 'schema', 'port', and 'login', and a 'relabeling' key where relabeling['password'] == 'Auth Token'.

*   MCPToolset must accept a conn_id positional argument and an optional tool_prefix keyword argument. Its id attribute must equal 'mcp-{conn_id}'. Its _tool_prefix attribute must store the tool_prefix value.

*   MCPToolset._get_server() must create an MCPHook with mcp_conn_id=conn_id and tool_prefix=self._tool_prefix, call get_conn() on it, cache the result, and return the same server object on subsequent calls.

*   MCPToolset.get_tools(ctx) must be an async method that delegates to server.get_tools(ctx) and returns its result.

*   MCPToolset.call_tool(tool_name, args, ctx, tool) must be an async method that delegates to server.call_tool(tool_name, args, ctx, tool) and returns its result.


*   Interface details: Type: Class
Name: MCPHook
Location: providers/common/ai/src/airflow/providers/common/ai/hooks/mcp.py
Description: Airflow hook that manages connections to MCP (Model Context Protocol) servers. Supports HTTP/streamable-HTTP, SSE, and stdio transports. Lazily imports MCP server classes inside get_conn().
Signature:
  __init__(self, mcp_conn_id: str = "mcp_default", tool_prefix: str | None = None)
  get_conn(self) -> Any  # Returns an MCP server object; result is cached on the hook instance
  test_connection(self) -> tuple[bool, str]  # Returns (True, message) on valid config, (False, message) on invalid
  get_ui_field_behaviour(cls) -> dict  # classmethod; returns hidden_fields and relabeling config

Type: Class
Name: MCPToolset
Location: providers/common/ai/src/airflow/providers/common/ai/toolsets/mcp.py
Description: Toolset adapter that wraps an MCPHook and exposes its server's tools. Has an id attribute formatted as "mcp-{conn_id}" and a _tool_prefix attribute. Delegates get_tools and call_tool to the underlying MCP server.
Signature:
  __init__(self, conn_id: str, tool_prefix: str | None = None)
  _get_server(self) -> Any  # Returns cached MCP server via MCPHook(mcp_conn_id=conn_id, tool_prefix=self._tool_prefix).get_conn()
  get_tools(self, ctx: Any) -> Awaitable[Any]  # async; delegates to server.get_tools(ctx)
  call_tool(self, tool_name: str, args: dict, ctx: Any, tool: Any) -> Awaitable[Any]  # async; delegates to server.call_tool(tool_name, args, ctx, tool)


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.