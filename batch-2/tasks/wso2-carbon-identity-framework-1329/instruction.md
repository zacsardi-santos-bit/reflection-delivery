Implement the ability for JavaScript-based authentication scripts to read and manipulate user claims during the authentication flow. Ensure that scripts can access and modify the current authentication subject's claims, and that any changes persist after the flow completes.

*   Update the `JsAuthenticationContext` class:
    *   Add a `subject` property returning a `JsAuthenticatedUser` for `context.getSubject()`.
*   Modify the `JsAuthenticatedUser` class:
    *   Add a `claims` property returning a `JsClaimSet` wrapping `getWrapped().getUserAttributes()`.
*   Enhance the `JsClaimSet` class:
    *   Wrap a `Map<ClaimMapping, String>` and expose:
        *   A `local` property returning a `JsClaimView` keyed by local claim URI.
        *   A `remote` property returning a `JsClaimView` keyed by remote claim URI.
        *   A `push` property as a consumer function accepting a claim object.
    *   Implement `push` to construct a new `ClaimMapping` and insert it into the user attributes map if the claim object contains `local`, `remote`, and `value` fields.
    *   Ensure no entry is added if the `value` field is missing.
    *   Provide an `isEmpty()` method returning true when the user attributes map is empty.
*   Develop the `JsClaimView` class:
    *   Provide array-like access to the map, allowing reading and writing by claim URI.
    *   Implement `getMember(String name)` to return the claim value or null.
    *   Implement `setMember(String name, Object value)` to update claim entries.
    *   Ensure `isArray()` returns true.
*   Define the `JsClaimView.IdMapper` interface:
    *   Include a `toId(ClaimMapping claimMapping) -> String` method for URI extraction.
*   Update the `JsLogger` class:
    *   Provide a static `getInstance()` method returning a singleton `JsLogger` instance.
    *   Use this instance in `JsClaimSet` to log errors for malformed claims.
*   Define constants in `FrameworkConstants.JSAttributes`:
    *   `JS_AUTHENTICATED_SUBJECT = "subject"`
    *   `JS_USER_CLAIMS = "claims"`
    *   `JS_CLAIM_MEMBER_LOCAL = "local"`
    *   `JS_CLAIM_MEMBER_REMOTE = "remote"`
    *   `JS_CLAIM_MEMBER_PUSH = "push"`
    *   `JS_CLAIM_MEMBER_VALUE = "value"`
    *   `JS_CLAIM_MEMBER_URI = "uri"`
*   Ensure claims pushed via `context.subject.claims.push(...)` persist in `context.getSubject().getUserAttributes()` and are retrievable via `ClaimMapping.build(localUri, remoteUri, null, false)` after the flow completes.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.