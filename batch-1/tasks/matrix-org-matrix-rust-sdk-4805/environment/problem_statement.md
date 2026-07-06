## Description

The SDK's authentication module currently uses naming conventions tied to OpenID Connect (OIDC) even though the underlying protocol it implements is more accurately described as OAuth 2.0. This creates a terminology mismatch for developers integrating the SDK: the public API surface (types, methods, error variants, and module paths) all reference "OIDC" or "Oidc" even when the functionality is really a general OAuth 2.0 flow.

This inconsistency should be resolved by renaming the relevant types, methods, error variants, and module paths to use "OAuth" terminology instead of "OIDC" terminology, reflecting the actual protocol being used.

## Expected Behavior

- The authentication module path should reflect OAuth (not OIDC) naming
- The main authentication type returned by the client should be named to reflect OAuth
- The method on the client that returns the authentication handler should use OAuth naming
- Error types (both the top-level wrapper and the specific sub-types for registration errors, authorization code errors, and token refresh errors) should use consistent OAuth naming
- The variant of the cross-signing reset auth type for this authentication method should use OAuth naming
- The internal metadata cache field and its lookup key should use OAuth/server-oriented naming
- The method for fetching server metadata should use a name that doesn't tie it to a specific sub-standard

## Why This Matters

Developers using the SDK who are familiar with the OAuth 2.0 standard will be confused when they see OIDC-branded types and methods for what is actually a standard OAuth 2.0 flow. Consistent naming across the API surface makes the SDK easier to understand, reduces onboarding friction, and ensures the public API accurately reflects the standards being implemented. This also ensures error handling code and pattern matching on error variants uses the correct, future-proof names.
