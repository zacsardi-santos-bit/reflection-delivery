## Description

The ProxmoxVE integration currently only supports password-based authentication. Users who manage their Proxmox server using API tokens — a more secure, scoped authentication mechanism that avoids sharing the main account password — have no supported path to set up the integration with token credentials.

Additionally, the current setup flow collects all connection parameters and credentials in a single step, which makes it difficult to conditionally ask for different fields depending on the authentication type chosen. For example, token authentication requires a token name and secret instead of a password, and non-default authentication realms should only be prompted when they are actually relevant.

## Expected Behavior

- The setup flow should be split into two stages: first collect server connection details and the desired authentication type (password vs. API token), then collect the appropriate credentials for that type.
- Users choosing password authentication with the default realm should only be asked for a password.
- Users choosing API token authentication should be asked for a token identifier and a token secret.
- Users with a non-default authentication realm should be able to specify that realm during the credentials step.
- All four combinations (default realm + password, default realm + token, custom realm + password, custom realm + token) should be fully supported.
- Existing configurations must be automatically migrated to include the new authentication method field without breaking the existing connection.
- Re-authentication flows should similarly prompt for updated credentials appropriate to the previously configured authentication type, including realm and token fields where applicable.

## Why This Matters

Many Proxmox administrators follow the principle of least privilege and create dedicated API tokens with limited permissions for integrations. Without token support, these users either have to use their full admin password or cannot use the integration at all. Supporting API tokens makes the integration significantly more secure and aligns with Proxmox best practices.
