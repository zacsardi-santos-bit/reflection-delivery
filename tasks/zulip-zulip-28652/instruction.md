Refactor the markdown module to replace mutation-based functions with pure, value-returning functions. Implement the `render` function to process raw content strings and the `get_topic_links` function to extract links from topics without requiring external configuration input. Ensure all existing callers are updated to use the new function signatures.

*   Implement the `render` function in `web/src/markdown.js` or `web/src/markdown.ts`:
    *   Accept a `raw_content` string as input.
    *   Return an object with:
        *   `content`: rendered HTML string.
        *   `flags`: array of strings indicating mention-related flags such as 'mentioned', 'stream_wildcard_mentioned', and 'topic_wildcard_mentioned'.
        *   `is_me_message`: boolean set to true if `raw_content` starts with '/me ', otherwise false.
    *   Propagate exceptions from the underlying parser when unexpected parsing errors occur.

*   Implement the `get_topic_links` function in `web/src/markdown.js` or `web/src/markdown.ts`:
    *   Accept a `topic` string or `undefined` as input.
    *   Return an array of objects, each with `url` and `text` fields.
    *   Return an empty array if `topic` is `undefined`.
    *   Use the linkifier map stored by the `initialize` function, without requiring it as an argument.

*   Implement the `initialize` function in `web/src/markdown.js` or `web/src/markdown.ts`:
    *   Accept a `helper_config` object that includes at least a `get_linkifier_map` property.
    *   Store the configuration for use by `render` and `get_topic_links`.

*   Update the echo module for message insertion:
    *   For stream messages, call `render` with raw content and `get_topic_links` with the message topic to populate `topic_links`.
    *   For direct messages (non-stream), call `render` with raw content and set `topic_links` to an empty array.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.