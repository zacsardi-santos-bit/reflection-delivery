I'm working with the Payload MCP plugin and I've noticed it's missing several useful operations that I need for my AI assistant workflows.

*   The MCP plugin must register a 'countDocuments' tool with annotations: title 'Count Documents', destructiveHint: false, idempotentHint: true, openWorldHint: false, readOnlyHint: true. The tool's input schema must include a 'collectionSlug' property, a 'locale' property of type string, and a 'where' property for filtering. When called, it must return an object containing a 'totalDocs' number field reflecting the count of matching documents.

*   The MCP plugin must register a 'duplicateDocument' tool with annotations: title 'Duplicate Document', destructiveHint: false, idempotentHint: false, openWorldHint: false, readOnlyHint: false. The tool's input schema must include 'id' (the source document's identifier) and 'data' (optional override fields). When called successfully, it must return the newly created duplicate document with a different 'id' and any overridden field values applied.

*   The 'duplicateDocument' tool must be disabled by default for authentication collections (e.g., 'users'). When called on such a collection, the tool must return an error response with isError: true and include the text: 'MCP access to "duplicateDocument" is not enabled for collection "<collectionSlug>"'.

*   The MCP plugin must register a 'findDistinct' tool with annotations: title 'Find Distinct', destructiveHint: false, idempotentHint: true, openWorldHint: false, readOnlyHint: true. The tool's input schema must include a 'field' property. When called, it must return an object with a 'values' array containing entries for each distinct value of the specified field found in the collection.

*   The MCP plugin must register a 'countVersions' tool for version-enabled collections. Its input schema must include 'collectionSlug' and an optional 'where' filter. When called, it must return an object with a 'totalDocs' number field.

*   The MCP plugin must register a 'findVersions' tool with annotations: title 'Find Versions', destructiveHint: false, idempotentHint: true, openWorldHint: false, readOnlyHint: true. Its input schema must include 'collectionSlug' and an optional 'where' filter. When called, it must return an object with a 'docs' array of version records each containing an 'id' field.

*   The MCP plugin must register a 'findVersionByID' tool for version-enabled collections. Its input schema must include 'collectionSlug' and 'id'. When called with a valid version ID, it must return an object with 'id' matching the requested version and a 'version' object containing the versioned document fields.

*   The MCP plugin must register a 'restoreVersion' tool with annotations: title 'Restore Version', destructiveHint: true, idempotentHint: false, openWorldHint: false, readOnlyHint: false. Its input schema must include an 'id' property. When called with a version ID, it must return the restored document whose 'id' matches the original (parent) document's identifier.

*   All new collection tools (countDocuments, countVersions, duplicateDocument, findDistinct, findVersionByID, findVersions, restoreVersion) must appear in the MCP tool listing alongside existing tools such as createDocument and getCollectionSchema.

*   The MCP plugin must register a 'findGlobalVersions' tool with annotations: title 'Find Global Versions', destructiveHint: false, idempotentHint: true, openWorldHint: false, readOnlyHint: true. Its input schema must include 'globalSlug' and an optional 'limit'. When called, it must return an object with a 'docs' array of version records each containing an 'id' field.

*   The MCP plugin must register a 'restoreGlobalVersion' tool with annotations: title 'Restore Global Version', destructiveHint: true, idempotentHint: false, openWorldHint: false, readOnlyHint: false. Its input schema must include 'globalSlug' and 'id'. When called, it must return the restored global document fields.

*   The MCP plugin must register a 'countGlobalVersions' tool for version-enabled globals. Its input schema must include 'globalSlug'. When called, it must return an object with a 'totalDocs' number field.

*   The MCP plugin must register a 'findGlobalVersionByID' tool. Its input schema must include 'globalSlug' and 'id'. When called with a valid version ID, it must return an object with 'id' matching the requested version and a 'version' object containing the versioned global fields.

*   The 'findGlobalVersions' and 'restoreGlobalVersion' tools must appear in the MCP tool listing for globals that have versioning enabled.


*   Interface details: The following MCP tools must be registered by the plugin. Tests reference them by their exact names via the MCP protocol. Each entry lists the tool name, required annotation values, and required input schema properties.

---

Type: MCP Tool
Name: countDocuments
Location: packages/plugin-mcp/src/mcp/builtin/collections/
Description: Counts documents in a collection with optional filtering.
Annotations:
  title: "Count Documents"
  destructiveHint: false
  idempotentHint: true
  openWorldHint: false
  readOnlyHint: true
Input Schema Properties (required to exist):
  collectionSlug: string
  locale: string (optional)
  where: object (optional)
Returns: { totalDocs: number }

---

Type: MCP Tool
Name: duplicateDocument
Location: packages/plugin-mcp/src/mcp/builtin/collections/
Description: Duplicates a document in a collection, optionally overriding fields on the copy.
Annotations:
  title: "Duplicate Document"
  destructiveHint: false
  idempotentHint: false
  openWorldHint: false
  readOnlyHint: false
Input Schema Properties (required to exist):
  id: string | number (the source document's ID)
  data: object (optional override fields)
Returns: the duplicated document with a new id and applied overrides
Error behavior: when the tool is disabled for a collection, returns isError: true with message text containing 'MCP access to "duplicateDocument" is not enabled for collection "<collectionSlug>"'

---

Type: MCP Tool
Name: findDistinct
Location: packages/plugin-mcp/src/mcp/builtin/collections/
Description: Finds distinct values for a field across a collection.
Annotations:
  title: "Find Distinct"
  destructiveHint: false
  idempotentHint: true
  openWorldHint: false
  readOnlyHint: true
Input Schema Properties (required to exist):
  field: string
Returns: { values: Array<{ [fieldName]: value }> }

---

Type: MCP Tool
Name: countVersions
Location: packages/plugin-mcp/src/mcp/builtin/collections/
Description: Counts document versions in a version-enabled collection.
Input Schema Properties (required to exist):
  collectionSlug: string
  where: object (optional)
Returns: { totalDocs: number }

---

Type: MCP Tool
Name: findVersions
Location: packages/plugin-mcp/src/mcp/builtin/collections/
Description: Lists versions for documents in a version-enabled collection.
Annotations:
  title: "Find Versions"
  destructiveHint: false
  idempotentHint: true
  openWorldHint: false
  readOnlyHint: true
Input Schema Properties (required to exist):
  collectionSlug: string
  where: object (optional)
Returns: { docs: Array<{ id: string | number }> }

---

Type: MCP Tool
Name: findVersionByID
Location: packages/plugin-mcp/src/mcp/builtin/collections/
Description: Retrieves a specific version record by its ID from a version-enabled collection.
Input Schema Properties (required to exist):
  collectionSlug: string
  id: string
Returns: { id: string | number, version: { [fields] } }

---

Type: MCP Tool
Name: restoreVersion
Location: packages/plugin-mcp/src/mcp/builtin/collections/
Description: Restores a specific version as the current document content.
Annotations:
  title: "Restore Version"
  destructiveHint: true
  idempotentHint: false
  openWorldHint: false
  readOnlyHint: false
Input Schema Properties (required to exist):
  id: string
Returns: restored document whose id matches the original (parent) document's id

---

Type: MCP Tool
Name: countGlobalVersions
Location: packages/plugin-mcp/src/mcp/builtin/globals/
Description: Counts versions for a version-enabled global.
Input Schema Properties (required to exist):
  globalSlug: string
Returns: { totalDocs: number }

---

Type: MCP Tool
Name: findGlobalVersions
Location: packages/plugin-mcp/src/mcp/builtin/globals/
Description: Lists versions for a version-enabled global.
Annotations:
  title: "Find Global Versions"
  destructiveHint: false
  idempotentHint: true
  openWorldHint: false
  readOnlyHint: true
Input Schema Properties (required to exist):
  globalSlug: string
  limit: number (optional)
Returns: { docs: Array<{ id: string | number }> }

---

Type: MCP Tool
Name: findGlobalVersionByID
Location: packages/plugin-mcp/src/mcp/builtin/globals/
Description: Retrieves a specific version record by its ID for a version-enabled global.
Input Schema Properties (required to exist):
  globalSlug: string
  id: string
Returns: { id: string | number, version: { [fields] } }

---

Type: MCP Tool
Name: restoreGlobalVersion
Location: packages/plugin-mcp/src/mcp/builtin/globals/
Description: Restores a specific version as the current content for a global.
Annotations:
  title: "Restore Global Version"
  destructiveHint: true
  idempotentHint: false
  openWorldHint: false
  readOnlyHint: false
Input Schema Properties (required to exist):
  globalSlug: string
  id: string
Returns: the restored global document fields


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.