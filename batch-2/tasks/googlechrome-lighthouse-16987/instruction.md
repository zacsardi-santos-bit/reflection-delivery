I'm working on a set of audits for checking model context protocol support on web pages.

*   The WebMCP gatherer class must be renamed and moved to core/gather/gatherers/webmcp.js as the default export named WebMCP. Its getArtifact() method must return an object with shape {isSupported: boolean, tools: Array} instead of a plain array.

*   When the WebMCP CDP enable command fails with an error message containing "'WebMCP.enable' wasn't found", the gatherer's startInstrumentation() must catch the error, record the unsupported state, and return without throwing. Errors with other messages must still be re-thrown.

*   The gatherer's getArtifact() must call context.driver.executionContext.evaluate() to check whether the model context interface is available on the page. If it returns false, the method must return {isSupported: false, tools: []}.

*   If the internal isSupported flag is false (due to the enable command failing) the gatherer must also return {isSupported: false, tools: []} from getArtifact().

*   When both the enable command succeeds and the evaluate check returns true, getArtifact() must return {isSupported: true, tools: resolvedTools} where resolvedTools is the deduplicated, nodeDetails-resolved list collected from events.

*   All three WebMCP audits (webmcp-form-coverage, webmcp-registered-tools, webmcp-schema-validity) must consume the WebMCP artifact (object with isSupported and tools fields) instead of the old WebMCPTools artifact (plain array).

*   Each WebMCP audit must return {score: 1, notApplicable: true} when artifacts.WebMCP.isSupported is false.

*   The form coverage audit must return {score: 1, notApplicable: true} when the forms list is empty or when all forms already have WebMCP annotations (previously these cases returned {score: 1} without notApplicable).

*   The schema validity audit must return {score: 1, notApplicable: true} when both artifacts.WebMCP.tools is empty and there are no schema issues.

*   The test artifact fixture at core/test/results/artifacts/artifacts.json must be updated: the key WebMCPTools must be renamed to WebMCP and its value must change from a plain array to an object {"isSupported": true, "tools": [...]} containing the same tool entries.


*   Interface details: Type: Class
Name: WebMCP
Location: core/gather/gatherers/webmcp.js
Description: Gatherer that collects WebMCP tool registration data and detects browser/page support. Renamed and refactored from the old gatherer (previously named WebMCPTools at webmcp-tools.js). Must be the default export. The class must have a `startInstrumentation(context)` method that enables the WebMCP CDP protocol; if the enable command fails with an error whose message includes `'WebMCP.enable' wasn't found`, it must set an internal flag indicating unsupported (instead of throwing). The class must have a `stopInstrumentation(context)` method to disable the protocol. The class must have a `getArtifact(context)` method that evaluates whether the page's model context interface is available via `context.driver.executionContext.evaluate()`; if the evaluate returns false OR if the enable command previously failed, it returns `{isSupported: false, tools: []}`. If both checks succeed, it returns `{isSupported: true, tools: resolvedTools}` where resolvedTools is the deduplicated list of tools with backendNodeId resolved to nodeDetails.

Artifact Shape:
The artifact produced by WebMCP.getArtifact() must be an object of the form:
  { isSupported: boolean, tools: Array<WebMCPTool> }
This replaces the previous plain array artifact keyed as WebMCPTools.

Artifact fixture update:
File: core/test/results/artifacts/artifacts.json
The key `WebMCPTools` must be renamed to `WebMCP` and its value must change from a plain array to an object with the shape `{ "isSupported": true, "tools": [...] }` where the tools array contains the same tool objects as before.

Audit: WebMcpFormCoverage
Location: core/audits/webmcp-form-coverage.js
The audit's requiredArtifacts must include 'WebMCP' (in addition to 'Inputs'). The audit() method must:
- Return `{notApplicable: true, score: 1}` when `artifacts.WebMCP.isSupported === false`
- Return `{notApplicable: true, score: 1}` when forms array is empty
- Return `{notApplicable: true, score: 1}` when all forms have WebMCP annotations (no forms lack tools)
Previously the last two cases returned `{score: 1}` without `notApplicable`.

Audit: WebMCPRegisteredTools
Location: core/audits/webmcp-registered-tools.js
The audit's requiredArtifacts must use 'WebMCP' instead of 'WebMCPTools'. The audit() method must:
- Return `{notApplicable: true, score: 1}` when `artifacts.WebMCP.isSupported === false`
- Access tools via `artifacts.WebMCP.tools` instead of `artifacts.WebMCPTools`

Audit: WebMcpSchemaValidity
Location: core/audits/webmcp-schema-validity.js
The audit's requiredArtifacts must use 'WebMCP' instead of 'WebMCPTools'. The audit() method must:
- Return `{notApplicable: true, score: 1}` when `artifacts.WebMCP.isSupported === false`
- Access tools via `artifacts.WebMCP.tools` instead of `artifacts.WebMCPTools`
- Return `{notApplicable: true, score: 1}` when both `artifacts.WebMCP.tools` is empty and there are no schema issues


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.