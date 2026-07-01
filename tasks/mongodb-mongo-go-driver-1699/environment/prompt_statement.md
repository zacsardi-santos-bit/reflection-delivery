I'm working on the MongoDB Go driver and I need to add support for converting BSON documents, arrays, and values to strings with a configurable byte-length limit. Right now, converting any BSON structure to a string always generates the full representation — for large documents (hundreds of thousands of keys, huge arrays, or megabyte strings) this is very slow and memory-intensive even when we only need the first few hundred bytes for logging.

I'd like to add a bounded stringification method to the document, array, and value types in the core BSON package. When given a positive byte limit, it should return the Extended JSON representation truncated to at most that many bytes. For zero or negative limits, it should return an empty string. When the full representation fits within the limit, it should return the complete string.

I also need a shared string-truncation utility in an internal package that correctly handles multi-byte Unicode characters — stopping at the last complete character that fits within the byte limit, without appending any ellipsis. The width parameter for this utility should be a regular signed integer.

Finally, the logging package needs a function to format a raw BSON document into a bounded string for command logging. This replaces the current approach which first converts the entire document to a string and then truncates.
