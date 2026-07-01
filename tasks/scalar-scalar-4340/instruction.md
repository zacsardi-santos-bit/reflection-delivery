Implement a Dart HTTP snippet generator plugin for the snippet generation library. Ensure the plugin can produce complete, runnable Dart source files from a given request description. Handle various HTTP request patterns and edge cases as specified.

Requirements:

*   Implement the `dartHttp` plugin with the following method signature:
    *   `generate(request: { url: string, method?: string, headers?: Array<{name: string, value: string}>, queryString?: Array<{name: string, value: string}>, cookies?: Array<{name: string, value: string}>, postData?: { mimeType: string, text?: string, params?: Array<{name: string, value?: string, fileName?: string}> } }, options?: { auth?: { username: string, password: string } | undefined }) -> string`
*   Ensure the generated Dart code:
    *   Begins with `import 'package:http/http.dart' as http;` and a blank line, followed by `void main() async {`.
    *   Ends with `  print(response.body); }`.
    *   Defaults to a GET request if no method is provided, with methods always in lowercase.
*   Handle headers:
    *   Include a `final headers = <String,String>{...};` block only if there are non-empty values.
    *   Exclude headers with empty values and prioritize the last occurrence of duplicate names.
*   Handle query string parameters:
    *   URL-encode and append them to the URL.
*   Handle cookies:
    *   URL-encode and include them as a `Cookie` header entry if non-empty.
*   Handle authentication:
    *   Include an `Authorization` header with base64-encoded credentials only if both username and password are provided.
*   Handle postData based on mimeType:
    *   `application/json`: Use Dart raw string literals.
    *   `application/x-www-form-urlencoded`: URL-encode and join parameters.
    *   `multipart/form-data`: Use param's `fileName` or `value`.
    *   `application/octet-stream`: Include text verbatim.
*   Ensure the order of blocks:
    *   Headers block first, then body block, followed by the response line.
    *   Append `, headers: headers` and `, body: body` to the response call as needed.
*   Handle URLs:
    *   Pass empty, extremely long, or special character-containing URLs verbatim to `Uri.parse('...')`.
*   Export the plugin from `http.ts` and re-export from `index.ts` in the same directory.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.