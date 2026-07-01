I'm working on a credential issuance service and need to update how the issuer publishes its supported credential types and configuration metadata.

Right now, the list of supported credential types is stored as a collection of untyped key-value maps. This means to access something as simple as the credential format, I have to do an unsafe type assertion on a map entry, which is error-prone and makes the code hard to maintain. I want to replace this with a proper typed structure that has named, strongly-typed fields for things like the credential id, format, types, credential subject, and display information.

Related to this, the well-known endpoint that exposes the issuer's configuration needs to be updated to follow the current specification. The supported credential types should be returned as a keyed map where each entry describes the cryptographic methods, proof types, display metadata, and credential definition for a specific credential type. Currently the response uses an older flat array format.

There's also an issue with how various configuration fields are returned. Fields like grant types, scopes, and the pre-authorized access flag are always present in the response even when they haven't been configured, making it impossible to tell whether a value is intentionally set or just a zero value. These fields should be optional so that absent configuration is clearly communicated to clients.

Finally, when the issuer configuration is signed, the endpoint currently returns the raw signed token with a token-specific content type. It should instead return a JSON document that wraps the signed value, using a standard JSON content type. This makes the endpoint response format consistent whether or not signing is enabled.

I also need to make sure that when dynamic client registration is enabled for a profile, the registration endpoint URL is included in the configuration response.
