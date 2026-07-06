## Description

The tool supports multiple spec formats for defining service-level objectives, but currently has no way to efficiently identify which format a given YAML document belongs to before attempting to fully parse it. When processing input files, the tool tries each available spec parser in sequence until one succeeds — a brute-force approach that is fragile and produces poor error messages when none match.

Each spec format has distinct identifying fields in its YAML header (version identifiers and resource kind indicators) that could be used to quickly and reliably determine the correct parser before attempting any full parse.

## Expected Behavior

- Each spec loader should expose a method to check whether a given YAML document matches its expected format, based on the presence and value of header fields.
- The detection must work correctly regardless of whether field values are wrapped in double quotes, single quotes, or no quotes at all.
- The detection must work even when there is extra whitespace around the field values.
- An empty document, a malformed document, or a document with unrecognized version/kind values should not be identified as a known spec type.

## Why This Matters

Without spec type detection, the system cannot clearly distinguish which parser to apply to a given document. Adding this capability allows the generate and validate commands to route documents to the correct handler directly, improving reliability and enabling clearer error reporting when an unknown spec format is encountered.
