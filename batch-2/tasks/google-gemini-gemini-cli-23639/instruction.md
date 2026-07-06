I'm working on the remote agent configuration system.

*   When a remote agent YAML frontmatter contains an auth section with type 'oauth' (not 'oauth2'), the agent loader must parse it successfully and return an auth object with type equal to 'oauth'

*   When parsing a remote agent with OAuth auth that includes optional fields (client_id, client_secret, scopes, authorization_url, token_url), all fields must be preserved in the parsed output alongside type 'oauth'

*   A minimal OAuth auth config containing only the type field set to 'oauth' (with no other fields) must be accepted as valid

*   When an OAuth auth config contains an invalid (non-URL) value for authorization_url, parsing must fail with a validation error, regardless of the auth type being 'oauth'

*   When an OAuth auth config contains an invalid (non-URL) value for token_url, parsing must fail with a validation error, regardless of the auth type being 'oauth'

*   The markdownToAgentDefinition conversion function must accept an auth config with type 'oauth' as a TypeScript string literal and pass it through correctly in the resulting agent definition


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.