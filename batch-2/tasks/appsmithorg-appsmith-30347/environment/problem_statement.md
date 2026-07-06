## Description

When a REST API action is configured with a JSON body, the plugin is re-serializing the body before sending it to the upstream server. This causes the transmitted body to differ from what was configured — keys may be reordered, and number representations may change (e.g., integers becoming floating-point numbers).

This is particularly problematic for APIs that require a specific key ordering for authentication (e.g., those using body-derived HMAC signatures) or that are sensitive to exact number types. The user configures a body expecting it to be sent verbatim, but the actual request carries a different payload.

## Expected Behavior

- If the configured body is already valid JSON, it should be transmitted to the upstream server exactly as-is, without any parse-and-re-serialize step. Key order and value types must be preserved.
- If the configured body contains numbers (whether in arrays or as the top-level value), they should be preserved with their original representation — integers stay integers, decimals stay decimals.
- If the configured body is not strictly valid JSON (e.g., contains a trailing comma), it is acceptable to re-parse and normalize it; however, the result should still be functionally correct JSON.

## Why This Matters

APIs using request body signing (e.g., AWS Signature v4, custom HMAC schemes) will reject requests if the body is modified after signing. Even small changes such as key reordering or number reformatting can cause signature mismatches, leading to authentication failures that are very difficult to diagnose.
