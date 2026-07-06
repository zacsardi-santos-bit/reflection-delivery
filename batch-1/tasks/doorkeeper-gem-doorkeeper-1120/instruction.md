Implement support for distinguishing between confidential and public OAuth client applications in your library. Update the authentication logic to allow public applications to obtain tokens without a client secret, while maintaining the requirement for confidential applications to provide both a client identifier and secret.

*   Update the Application model:
    *   Validate that the `confidential` field is explicitly set to either true or false; a nil value should make the application invalid.
    *   Implement a `confidential` method in `lib/doorkeeper/orm/active_record/application.rb` that returns true if the application is confidential and false if it is public.
    *   Implement a `confidential?` method as an alias to `confidential` in the same file.
    *   Implement the `Application.supports_confidentiality?` class method to check for the existence of the 'confidential' column in the `oauth_applications` table.

*   Modify the `by_uid_and_secret` method in `lib/doorkeeper/models/application_mixin.rb`:
    *   Ensure it returns a confidential application only when both the UID and secret match.
    *   Ensure it returns a non-confidential application when the UID matches and the secret is blank/nil; return nil for incorrect non-blank secrets.

*   Update the Credentials struct in `lib/doorkeeper/oauth/client/credentials.rb`:
    *   Ensure the `blank?` method returns true only when the UID is blank, not considering the secret.

*   Create a database migration:
    *   Add a boolean 'confidential' column to the `oauth_applications` table with `null: false` and `default: true` in `spec/dummy/db/migrate/20180210183654_add_confidential_to_application.rb`.

*   Adjust token revocation logic:
    *   Ensure the endpoint returns HTTP 200 and revokes tokens for non-confidential applications without client authentication.
    *   Ensure the endpoint returns HTTP 200 and revokes tokens for confidential applications only when the authenticated client matches.
    *   Ensure the endpoint returns HTTP 200 but does not revoke tokens if the authenticated client does not match for confidential applications.

*   Update the password credentials flow:
    *   Issue a new access token to a non-confidential client when only the client_id is provided.
    *   Reject requests with incorrect secrets for non-confidential clients.
    *   Reject confidential clients that omit the client_secret and issue tokens only when correct credentials are provided.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.