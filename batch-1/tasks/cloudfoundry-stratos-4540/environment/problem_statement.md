# Add Configurable Access Control for API Keys

## Description

The Stratos API currently supports API keys for authentication, but there is no way to control who is allowed to use them. We need a configuration option that lets administrators choose between three modes: API keys completely disabled, API keys restricted to admin users only, or API keys enabled for all users.

## Expected Behavior

- When API keys are configured as disabled, any attempt to create, list, or delete API keys should be rejected with a "forbidden" error message.
- When API keys are configured for admins only, non-admin users should be rejected when trying to manage API keys, and the API key authentication middleware should ignore tokens presented by non-admin users.
- When API keys are enabled for all users, existing behavior should be preserved.
- The session verification endpoint should include the current API keys access setting in its response, so clients can adapt their UI accordingly.
- The API key authentication middleware should respect the configuration setting: if API keys are disabled, it should skip key-based auth entirely; if restricted to admins, it should only authenticate requests whose associated key belongs to an admin user.

## Why This Matters

Operators need flexibility to roll out API key support gradually or restrict it to trusted admin users only, rather than having an all-or-nothing switch. Without this, there is no way to prevent non-admin users from creating API keys once the feature is enabled.
