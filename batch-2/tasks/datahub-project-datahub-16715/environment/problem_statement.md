## Description

The DataHub CLI configuration command currently supports two authentication methods: providing a personal access token directly or authenticating with a username and password. However, organizations that rely on browser-based single sign-on have no way to use the CLI to generate and save a token automatically — users must manually navigate to the web UI to create a token and then copy it back into their CLI setup.

## Expected Behavior

- The configuration command should support a browser-based SSO login mode that opens a browser window, lets the user complete their identity provider login, and automatically generates and stores an access token in the config file.
- The SSO mode should be mutually exclusive with providing credentials (username/password or token) directly. Attempting to combine them should produce a clear error message.
- The SSO mode should support a companion flag for special support-access use cases; that flag should require the SSO mode to also be active — using it alone should produce a clear error.
- The default token duration should vary based on whether the host is a local development instance or a cloud-hosted deployment.
- When SSO login succeeds, the generated token name and the host URL should be saved to the config file, and the user should see a confirmation message.
- If previous CLI-generated tokens already exist for the user, a warning with a link to manage them should be printed so the user knows to clean up stale credentials.

## Why This Matters

Users in SSO-enforced environments cannot authenticate via username/password, and manually creating tokens through the web UI adds friction. A browser-driven SSO flow in the CLI removes that barrier and makes it straightforward to configure access in such environments.
