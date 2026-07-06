Ensure that server-rendered pages in SvelteKit properly escape data fetched during the load phase to prevent cross-site scripting vulnerabilities. Implement a solution that safely encodes potentially dangerous characters in the data to be displayed as literal text without executing any scripts.

*   Escape all fetched data during the server-side rendering phase to prevent HTML script-breaking content from executing.
    *   Ensure that data containing characters like '</script><script>' is displayed as literal text.
    *   Prevent any unintended script execution in server-side rendered mode, especially when JavaScript is disabled.
*   Modify the framework's serialization process for fetch response bodies.
    *   Ensure that characters such as '<', '>', and '/' are properly escaped.
    *   Guarantee that the serialized output cannot be interpreted as HTML tag boundaries when embedded in a script element.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.