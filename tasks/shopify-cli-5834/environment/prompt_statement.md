I'm working on the Shopify theme development server and running into two related issues with Liquid file parsing.

First, when any theme section file contains Liquid syntax errors, the server crashes while trying to aggregate stylesheet and javascript content for the compiled asset endpoints. Instead of failing entirely, I'd like it to fall back to a simpler extraction approach for files that can't be fully parsed, include whatever valid content it can extract, and log a debug warning about the parsing issue.

Second, the hot-reload change-detection logic for section files only distinguishes between stylesheet and javascript block changes — it doesn't separately track whether the schema block or the remaining liquid content changed. I'd like it to track all four content parts (stylesheet, javascript, schema, and remaining liquid) independently, so that hot-reload events carry accurate information about exactly which parts of a file changed. I also need the same fault-tolerant parsing behavior here: if a file has syntax errors, the system should still attempt to detect which parts changed using a fallback approach and log a debug warning.

On the other side of this, hot-reload events for JSON files and pure asset files are currently including file-part change metadata that doesn't apply to those file types. That metadata should be omitted for those file types.

The internal cache used by the change-detection logic should also be exported so it can be cleared externally between uses. The cache should store normalized versions of each content part (with whitespace collapsed) and use the file checksum to avoid redundant processing.
