## Description

The utility that converts theme objects to CSS custom properties does not gracefully handle the case where the theme data passed to it is absent (undefined). When a theme section is missing or has not been initialized, calling this utility crashes or produces unexpected behavior instead of returning an empty result.

## Expected Behavior

- When the theme object (or a portion of it) is undefined, the conversion utility should return an empty object rather than throwing an error or producing unexpected output.
- Callers should not need to guard every invocation against missing theme data — the utility should be resilient to undefined inputs.

## Why This Matters

In practice, there are situations where a theme section (such as colors) may not yet be defined, or may be undefined due to dynamic theme composition. The conversion utility should handle these cases gracefully so that components relying on it do not crash when theme data is partially or fully unavailable.
