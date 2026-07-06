I noticed that when our session context gets compacted due to a long conversation, the compaction request sent to the model is missing some important configuration that the normal conversation requests include.

*   When a compact (conversation summarization) request is made, the request body must include a 'tools' field whose value exactly matches the 'tools' field sent in the initial responses API request for that session.

*   When a compact request is made, the request body must include a 'parallel_tool_calls' field whose value exactly matches the 'parallel_tool_calls' field sent in the initial responses API request for that session.

*   When a compact request is made, the request body must include a 'reasoning' field whose value exactly matches the 'reasoning' field sent in the initial responses API request for that session.

*   When a compact request is made, the request body must include a 'text' field (text controls) whose value exactly matches the 'text' field sent in the initial responses API request for that session.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.