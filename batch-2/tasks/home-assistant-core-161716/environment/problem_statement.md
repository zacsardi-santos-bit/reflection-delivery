## Description

The ecobee integration currently only supports a PIN-based setup flow that requires users to obtain an API key and complete a separate authorization step on the ecobee website. There is no way to set up the integration using a standard ecobee account username and password. Many users would prefer to authenticate with their credentials directly rather than navigating the multi-step API key and PIN process.

## Expected Behavior

- Users should be able to enter their ecobee account username and password in the initial setup form to authenticate directly.
- If credential authentication succeeds, the integration should be fully configured and ready to use without any additional steps.
- If credential authentication fails, the user should see a clear error on the setup form and be able to correct their credentials and try again.
- If a user provides both an API key and credentials but authentication still fails, a distinct error should be shown to indicate the combination is not valid.
- After a failed authentication attempt, the user should be able to retry and successfully complete the setup.

## Why This Matters

Users who don't want to deal with API keys and PIN flows should have a simpler path to set up the ecobee integration using only their account credentials. The current flow is unnecessarily complex for users who just want to connect their ecobee thermostat to Home Assistant.
