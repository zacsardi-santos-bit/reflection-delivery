## Description

The Snowflake database connection currently only supports username/password authentication. However, Snowflake supports multiple authentication methods that are widely used in enterprise environments: browser-based single sign-on (SSO), multi-factor authentication (MFA push via Duo), key pair (certificate-based) authentication, and OAuth tokens or personal access tokens (PAT).

Users who connect to Snowflake via any of these methods cannot configure them through the connection editor, and the generated Python connection code does not reflect their actual authentication setup.

## Expected Behavior

The Snowflake connection should allow users to select an authentication type from:
- **Password** — standard username and password, with an optional MFA (push) toggle
- **SSO (Browser)** — browser-based single sign-on using an external browser authenticator
- **Key Pair** — certificate-based authentication with a path to the private key file and an optional passphrase
- **OAuth / PAT** — token-based authentication using an OAuth token or personal access token

Each authentication type should generate the correct Python connection code, including the appropriate connection arguments for the chosen method. Optional fields like warehouse, schema, and role should only appear in the generated code when they are provided.

## Why This Matters

Many organizations using Snowflake do not permit simple password authentication and require SSO, MFA, or certificate-based access. Without these options, users cannot generate working connection code directly from the notebook editor.
