## Description

When users configure proxy buffer settings and have the directive auto-adjust feature disabled, they expect their exact configuration values to be passed through to NGINX without modification. However, the ingress controller has been silently normalizing and correcting these values internally regardless of the auto-adjust setting. This means the values actually applied to NGINX may differ from what the user specified.

Additionally, when a size-type annotation is set to an invalid value, the error message only says "must be a size" — which is not helpful enough for users to understand what format is actually expected.

## Expected Behavior

- When directive auto-adjust is **disabled**, proxy buffer configuration values should be passed through to NGINX exactly as the user specified, without any normalization or correction.
- When directive auto-adjust is **enabled**, validation and normalization should still occur (e.g., normalizing size unit case, replacing unsupported units with a valid default).
- Invalid proxy buffer settings with auto-adjust enabled should be rejected with a clear error, not silently modified.
- Error messages for invalid size annotations should clearly describe the accepted format — including valid unit suffixes and examples — so users can quickly identify and fix the issue.

## Why This Matters

Users who set directive auto-adjust to disabled are explicitly opting out of automatic corrections. Silently modifying their values undermines that choice and can lead to unexpected NGINX behavior. Better error messages reduce the time spent debugging configuration issues.
