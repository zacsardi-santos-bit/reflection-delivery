## Description

The Azure Assistant provider builds cache keys that embed sensitive data in plaintext. Right now, the cache key contains the user's prompt, assistant system instructions, authentication credentials, and Azure endpoint hostnames as raw strings. This means anyone with read access to the cache storage — or anyone watching debug logs — can trivially read these values, including secrets like API keys.

## Expected Behavior

- Cache keys should be opaque: all sensitive inputs (prompt, instructions, auth credentials, endpoint URL) must be hashed before being used as part of the cache key, so the key itself reveals nothing about its inputs.
- The hash portion of the cache key should be exactly 64 lowercase hexadecimal characters.
- Different authentication credentials or different Azure endpoints must produce different cache keys, even for identical prompts.
- The same inputs must always produce the same cache key, even across process restarts or module reloads — caching must remain effective.
- Debug log messages must not include the prompt text; log messages should instead record non-sensitive metadata like prompt length.

## Why This Matters

Users may store prompts containing confidential business information, and their Azure credentials are clearly sensitive. Exposing these in cache keys or logs is a security and privacy risk. The cache should be able to identify repeated requests without storing any sensitive content in the key itself.
