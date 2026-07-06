I'm working on the link-aware feature in Bruno's code editor, and I've noticed that URLs with nested parentheses — like the long query URLs produced by tools such as Kibana — get truncated when they're displayed as clickable links.

*   The extendUrlWithBalancedParentheses function must be a named export from packages/bruno-app/src/utils/codemirror/linkAware.js.

*   The function must accept three arguments: the initially detected URL string, the full line of text string, and a numeric lastIndex representing the end position (exclusive) of the URL match within the line.

*   The function must return an object with a 'url' string property containing the (possibly extended) URL.

*   When the input URL already has balanced parentheses (equal number of opening and closing), the function must return the original URL unchanged.

*   When the input URL has more opening parentheses than closing ones (unbalanced), the function must extend the URL by consuming additional characters from the line starting at lastIndex until parentheses become balanced.

*   Extension must stop when a whitespace character is encountered in the line; characters after whitespace must not be appended to the URL.

*   Extension must stop before appending a closing parenthesis that would cause over-balancing — i.e., result in more closing parentheses than opening parentheses in the URL.

*   The function must correctly handle URLs with deeply nested parentheses, such as Kibana or RISON-style query strings, extending the URL all the way to the point where all open parentheses are properly closed.


*   Interface details: Type: Function
Name: extendUrlWithBalancedParentheses
Location: packages/bruno-app/src/utils/codemirror/linkAware.js
Signature: extendUrlWithBalancedParentheses(url, line, lastIndex)
Description: Takes an initially detected URL that may have been truncated (unbalanced parentheses), the full line of text, and the end position of the URL match in the line. Returns an object with a `url` string property containing the URL extended (if necessary) to balance any unmatched open parentheses. Stops extension at whitespace or when further extension would cause over-balancing (more closing parentheses than opening). Must be a named export from the module.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.