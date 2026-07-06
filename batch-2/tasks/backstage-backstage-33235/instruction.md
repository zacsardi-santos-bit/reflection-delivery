I'm working on the MCP actions backend plugin and need to add two features.

*   McpService.create() must accept an optional namespacedToolNames boolean parameter (default: true). When true, tool names exposed via tools/list must use the full action ID format (pluginId:actionName). When false, only the short action name is used.

*   McpService.getServer() must accept an optional serverConfig parameter of type McpServerConfig. When serverConfig is provided with includeRules and excludeRules arrays, the server must filter the available tools accordingly.

*   When serverConfig.includeRules is an empty array and serverConfig.excludeRules is an empty array, all available actions must be returned.

*   When serverConfig.includeRules contains rules with id patterns (e.g., 'catalog:*'), only actions whose IDs match the pattern must be listed. Glob patterns such as 'catalog:get-*' must be supported, matching only actions whose IDs match the glob.

*   When serverConfig.excludeRules contains rules with attribute conditions (e.g., { attributes: { destructive: true } }), actions matching those attributes must be excluded from the tools list.

*   When a tools/call request references a tool name that is outside the server's filtered set (i.e., not returned by tools/list for that server), the response must be { content: [{ type: 'text', text: 'Action "<name>" not found' }], isError: true }.

*   McpServerConfig must be a type exported from plugins/mcp-actions-backend/src/config.ts with fields: name (string), includeRules (FilterRule[]), excludeRules (FilterRule[]).

*   parseFilterRules() must be a function exported from plugins/mcp-actions-backend/src/config.ts that accepts a config array (from ConfigReader.getConfigArray) and returns a FilterRule array supporting id glob patterns and attribute conditions.

*   When mcpActions.servers is configured with named keys, each key must create a separate endpoint at /api/mcp-actions/v1/{key}. Each server endpoint must only expose actions whose IDs match the configured filter rules for that server.

*   When mcpActions.servers is not configured, the plugin must continue serving a single server at /api/mcp-actions/v1 with all actions available.

*   Actions registered by a backend plugin are identified by a namespaced ID of the form pluginId:actionName. By default, this full ID must be used as the MCP tool name.


*   Interface details: Type: Type
Name: McpServerConfig
Location: plugins/mcp-actions-backend/src/config.ts
Description: Configuration for a named MCP server, specifying a display name and include/exclude filter rules for scoping available actions.
Signature:
  {
    name: string;
    includeRules: FilterRule[];
    excludeRules: FilterRule[];
  }

Type: Type
Name: FilterRule
Location: plugins/mcp-actions-backend/src/config.ts
Description: A single filter rule that can match actions by ID (with glob pattern support) and/or by attributes such as destructive, readOnly, and idempotent.
Signature:
  {
    id?: string;
    attributes?: {
      destructive?: boolean;
      readOnly?: boolean;
      idempotent?: boolean;
    };
  }

Type: Function
Name: parseFilterRules
Location: plugins/mcp-actions-backend/src/config.ts
Description: Parses a config array (as returned by ConfigReader.getConfigArray) into an array of FilterRule objects. Supports id patterns with glob syntax and attribute conditions.
Signature: parseFilterRules(configs: Config[]) -> FilterRule[]

Type: Class
Name: McpService
Location: plugins/mcp-actions-backend/src/services/McpService.ts
Description: Service responsible for creating and managing MCP servers backed by registered backend actions.
Signature:
  static create(options: {
    actions: ActionsService;
    metrics: MetricsService;
    namespacedToolNames?: boolean;
  }) -> Promise<McpService>

  getServer(options: {
    credentials: BackstageCredentials;
    serverConfig?: McpServerConfig;
  }) -> Server


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.