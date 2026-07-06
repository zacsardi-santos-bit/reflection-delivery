Ensure that the Quarkus Jackson serialization infrastructure respects JSON property names defined on constructor parameters during serialization. Update the system so that classes using constructor parameter annotations for JSON property names are serialized with those names, maintaining symmetry between deserialization and serialization.

Requirements:
*   Modify the Quarkus Jackson serialization infrastructure to consider constructor parameter annotations for JSON property names.
    *   Update the build-time code generators in `extensions/resteasy-reactive/rest-jackson/deployment/src/main/java/io/quarkus/resteasy/reactive/jackson/deployment/processor/`.
    *   Ensure that if a field or getter lacks a `@JsonProperty` annotation but the constructor parameter has one, the constructor parameter's `@JsonProperty` value is used for serialization.
*   Ensure the `TokenResponse` class is serialized correctly:
    *   Located at `extensions/resteasy-reactive/rest-jackson/deployment/src/test/java/io/quarkus/resteasy/reactive/jackson/deployment/test/TokenResponse.java`.
    *   Contains final fields `String accessToken` and `Integer expiresIn`.
    *   Constructor: `TokenResponse(@JsonProperty("access_token") String accessToken, @JsonProperty("expires_in") Integer expiresIn)`.
    *   Public getters `getAccessToken()` and `getExpiresIn()` without `@JsonProperty` annotations.
*   Verify the REST endpoint functionality:
    *   Endpoint: `POST /simple/kotlin-data-echo`.
    *   Located in `SimpleJsonResource.java`.
    *   Method signature: `public TokenResponse echoKotlinData(TokenResponse tokenResponse)`.
    *   When called with JSON `{"access_token":"ABC","expires_in":3600}`, the response must be HTTP 200 with `Content-Type application/json`.
    *   Response body must contain `{"access_token":"ABC","expires_in":3600}` using snake_case names from constructor annotations.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.