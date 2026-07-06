I'm working on Streamlit's authentication module and need to add a dual-backend approach for creating and validating short-lived provider tokens used during OAuth sign-in.

*   encode_provider_token(provider) must produce a JWT containing a 'provider' string claim and an integer 'exp' (expiration) claim.

*   encode_provider_token must attempt the joserfc backend (_encode_provider_token_with_joserfc) first and fall back to the authlib backend (_encode_provider_token_with_authlib) when joserfc raises ImportError.

*   encode_provider_token must raise StreamlitAuthError with a message matching the pattern 'pip install streamlit[auth]' when both backends raise ImportError.

*   decode_provider_token(token) must return a dict with at minimum 'provider' (str) and 'exp' (int) fields.

*   decode_provider_token must attempt the joserfc backend (_decode_provider_token_with_joserfc) first and fall back to the authlib backend (_decode_provider_token_with_authlib) when joserfc raises ImportError.

*   decode_provider_token must raise StreamlitAuthError with a message matching 'expired' when the token's expiration time is already in the past.

*   decode_provider_token must raise StreamlitAuthError with a message matching 'provider claim is missing' when the 'provider' key is absent from the token claims.

*   decode_provider_token must raise StreamlitAuthError with a message matching 'provider claim is empty' when the 'provider' claim exists but is an empty string.

*   decode_provider_token must raise StreamlitAuthError with a message matching 'exp claim' when the 'exp' key is absent or when its value is not an integer.

*   decode_provider_token must raise StreamlitAuthError with a message matching 'pip install streamlit[auth]' when both backends raise ImportError.

*   _get_joserfc_signing_key must suppress joserfc SecurityWarnings by calling _ensure_joserfc_security_warning_suppressed before performing key operations, so that no SecurityWarning is propagated to callers.

*   _get_joserfc_signing_key must log exactly one warning via _LOGGER.warning containing the text '112 bits' when the signing secret is shorter than 14 bytes (112 bits); subsequent calls with the same short secret must not emit additional warnings.

*   _get_joserfc_signing_key must not emit any warning when the signing secret is of adequate length.

*   _warn_short_signing_secret_once must be a cached function (decorated with functools.lru_cache or equivalent) exposing a cache_clear() method, so the short-secret warning is emitted at most once per unique secret.

*   _ensure_joserfc_security_warning_suppressed must be idempotent: calling it multiple times must result in exactly one filter entry with action 'ignore' for joserfc.errors.SecurityWarning in the active warnings filter list.

*   _encode_provider_token_with_authlib(provider) and _decode_provider_token_with_authlib(token) must implement the full encode/decode round-trip using authlib, returning the same 'provider' string and an integer 'exp'.

*   When encoding and decoding a provider token via the public API (encode_provider_token / decode_provider_token) on the joserfc path, no Python warnings must be emitted to the warnings module.

*   The encode/decode round-trip via the Authlib fallback (public API with joserfc raising ImportError) must produce a valid payload with the original 'provider' string and an integer 'exp'.


*   Interface details: Type: Function
Name: encode_provider_token
Location: lib/streamlit/auth_util.py
Signature: encode_provider_token(provider: str) -> str
Description: Encodes a JWT provider token with a "provider" claim and an integer "exp" (expiration) claim. Attempts to use the joserfc backend first (_encode_provider_token_with_joserfc); if that raises ImportError, falls back to _encode_provider_token_with_authlib. If both raise ImportError, raises StreamlitAuthError with a message matching the regex `pip install streamlit\[auth\]`.

Type: Function
Name: decode_provider_token
Location: lib/streamlit/auth_util.py
Signature: decode_provider_token(token: str) -> dict
Description: Decodes a JWT provider token and returns a dict with at minimum "provider" (str) and "exp" (int) fields. Attempts joserfc backend first (_decode_provider_token_with_joserfc); falls back to _decode_provider_token_with_authlib on ImportError. If both raise ImportError, raises StreamlitAuthError matching `pip install streamlit\[auth\]`. Raises StreamlitAuthError with message matching "expired" if token is expired, "provider claim is missing" if "provider" key is absent, "provider claim is empty" if "provider" is an empty string, and "exp claim" if "exp" is absent or not an integer.

Type: Function
Name: _get_joserfc_signing_key
Location: lib/streamlit/auth_util.py
Signature: _get_joserfc_signing_key() -> <joserfc key object>
Description: Builds and returns a joserfc signing key from the current signing secret. When the secret is shorter than 112 bits (14 bytes), emits exactly one warning via _LOGGER.warning with a message containing "112 bits", using the caching mechanism of _warn_short_signing_secret_once. Suppresses joserfc SecurityWarning via _ensure_joserfc_security_warning_suppressed before key operations.

Type: Function
Name: _warn_short_signing_secret_once
Location: lib/streamlit/auth_util.py
Signature: _warn_short_signing_secret_once(...) -> None (cached, has cache_clear() method)
Description: A function decorated with functools.lru_cache (or equivalent) so that it emits the "112 bits" short-secret warning at most once per unique secret. Must expose a cache_clear() method.

Type: Function
Name: _ensure_joserfc_security_warning_suppressed
Location: lib/streamlit/auth_util.py
Signature: _ensure_joserfc_security_warning_suppressed() -> None
Description: Idempotently adds a "ignore" filter for joserfc.errors.SecurityWarning to the warnings filter list. Calling this function multiple times must result in exactly one matching filter entry (action "ignore", category SecurityWarning) in warnings.filters.

Type: Function
Name: _encode_provider_token_with_joserfc
Location: lib/streamlit/auth_util.py
Signature: _encode_provider_token_with_joserfc(provider: str) -> str
Description: Encodes a provider JWT using the joserfc library. Must be a patchable attribute on auth_util; raises ImportError if joserfc is unavailable.

Type: Function
Name: _decode_provider_token_with_joserfc
Location: lib/streamlit/auth_util.py
Signature: _decode_provider_token_with_joserfc(token: str) -> dict
Description: Decodes a provider JWT using the joserfc library. Must be a patchable attribute on auth_util; raises ImportError if joserfc is unavailable.

Type: Function
Name: _encode_provider_token_with_authlib
Location: lib/streamlit/auth_util.py
Signature: _encode_provider_token_with_authlib(provider: str) -> str
Description: Encodes a provider JWT using the authlib library. Must be a patchable attribute on auth_util; returns a JWT string.

Type: Function
Name: _decode_provider_token_with_authlib
Location: lib/streamlit/auth_util.py
Signature: _decode_provider_token_with_authlib(token: str) -> dict
Description: Decodes a provider JWT using the authlib library. Must be a patchable attribute on auth_util; returns a dict with at minimum "provider" (str) and "exp" (int) fields.

Type: Function
Name: _get_provider_token_expiration_timestamp
Location: lib/streamlit/auth_util.py
Signature: _get_provider_token_expiration_timestamp() -> int
Description: Returns the absolute Unix timestamp (integer seconds since epoch) at which a newly created provider token should expire. Must be a patchable attribute on auth_util.

Type: Attribute
Name: _LOGGER
Location: lib/streamlit/auth_util.py
Description: A module-level logger object (created via Python's logging module) that exposes a .warning(msg, ...) method. Used by _get_joserfc_signing_key to emit the short-secret warning.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.