## Description

The Shopify theme development server compiles stylesheet and javascript content from Liquid section files to serve aggregated assets locally, and it emits granular hot-reload events to the browser when files change. Both features fail entirely when any theme file contains Liquid syntax errors, rather than degrading gracefully. Additionally, the hot-reload change-detection logic doesn't track all relevant parts of a Liquid section file — it only distinguishes stylesheet and javascript changes, missing schema and remaining liquid content as separate tracked parts.

## Expected Behavior

- When a Liquid file contains syntax errors, the compiled asset endpoints (for both stylesheet and javascript aggregation) should still attempt to extract content from that file using a fallback approach, include any valid content in the output, and log a debug-level warning about the parsing failure instead of crashing.
- When detecting which parts of a section Liquid file have changed for a hot-reload event, the system should track four distinct content parts independently: stylesheet block, javascript block, schema block, and the remaining liquid content. Only the parts that actually changed should be flagged.
- Hot-reload events for JSON files and pure asset files (CSS, JS) should not include file-part change metadata, since these file types don't have the structured Liquid parts being tracked.
- The per-file content cache used for change detection should be exported so it can be managed externally (e.g., cleared between uses).

## Why This Matters

Theme authors frequently work with files that mix valid stylesheet or javascript blocks with otherwise syntactically broken Liquid. The current behavior causes the entire compiled asset to fail or the hot-reload system to throw errors, disrupting the local development experience. Making these systems fault-tolerant means developers can continue iterating even when parts of their theme have temporary syntax issues.
