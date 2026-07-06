Implement a feature in the HTTP client that allows users to configure the client to send HTTP/1.1 headers in title case format. Ensure that this is an optional setting, preserving the default behavior of sending headers in lowercase.

*   Add a method `http1_title_case_headers` to the client builder:
    *   Location: `src/client/mod.rs`
    *   Signature: `http1_title_case_headers(val: bool) -> &mut Self`
    *   Description: Configures the client to send headers in title case when set to true. Default is false.

*   Update the `Http1Transaction` trait's `encode` method:
    *   Location: `src/proto/mod.rs` and `src/proto/h1/role.rs`
    *   Signature: `encode(head: MessageHead<Self::Outgoing>, body: Option<BodyLength>, method: &mut Option<Method>, title_case_headers: bool, dst: &mut Vec<u8>) -> ::Result<Encoder>`
    *   Description: Include a `title_case_headers` parameter to determine header casing. The Client implementation must use this flag to decide between lowercase and title-case header writing. The Server implementation should accept the parameter but can ignore it.

*   Implement title case conversion for header names:
    *   Capitalize the first ASCII letter of each hyphen-separated word in the header name.
    *   Example: Convert 'content-length' to 'Content-Length' and 'content-type' to 'Content-Type'.
    *   Apply conversion only to lowercase ASCII letters (a-z).

*   Create a function `set_title_case_headers`:
    *   Location: `src/proto/h1/conn.rs`
    *   Signature: `set_title_case_headers(&mut self)`
    *   Description: Sets the title_case_headers flag to true on the connection state, ensuring subsequent `encode` calls use title case header writing.

*   Ensure the title case option is chainable with other builder settings and applies to actual outgoing connections.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.