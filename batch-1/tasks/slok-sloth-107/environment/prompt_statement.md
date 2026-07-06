I'm working on a tool that processes YAML files describing service-level objectives in multiple formats. Right now, when the tool receives a YAML document, it tries each available parser one by one until one of them succeeds — which is slow, fragile, and gives unhelpful errors when nothing matches.

Each supported format has distinctive identifying fields at the top of the YAML (like version or kind indicators) that uniquely identify which parser to use. I'd like each spec loader to have the ability to quickly check whether a given YAML document is "its" format, before doing a full parse. This check should handle field values written with double quotes, single quotes, no quotes at all, and extra whitespace around the values — and it should return false for empty documents, broken YAML, or documents with wrong version or kind values.

This way, the generate and validate commands can match a document to the right loader immediately instead of trial-and-erroring through all of them.
