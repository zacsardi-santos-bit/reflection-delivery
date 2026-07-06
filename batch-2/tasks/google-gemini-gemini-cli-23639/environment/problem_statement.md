## Description

Remote agents that use the interactive OAuth browser-based sign-in flow need a configuration type identifier in their YAML frontmatter. Currently this type is specified with a longer, versioned name, but it should be simplified to a shorter name to improve usability and consistency.

## Expected Behavior

- When configuring a remote agent with OAuth-based authentication, users should use the shorter identifier (without a version suffix) in the type field of the auth section
- The agent loader should parse and validate configurations using the new shorter identifier
- All existing optional fields for OAuth auth (such as client credentials and scopes) should continue to work normally with the new identifier
- Minimal configurations using only the type field should remain valid
- URL validation for optional OAuth endpoint fields should continue to work correctly

## Why This Matters

Users who define remote agents with OAuth authentication need a clear, stable, non-versioned identifier for the auth type. Using a version-suffixed name is confusing and implies a version-specific implementation when the type is intended to be the canonical name for this auth method. Updating to the shorter name makes the configuration cleaner and more intuitive.
