## Description

When establishing SSL connections, the library always overrides the TLS cipher list with its own hardcoded set of defaults, even on systems where the underlying SSL library is modern enough to provide secure cipher defaults on its own. This means platform administrators and system operators cannot rely on OS-level or SSL-library-level cipher policy, because the library's static list always takes precedence.

On newer versions of modern SSL/TLS libraries, the built-in cipher defaults are already strong and regularly updated. Forcing an explicit override prevents users from benefiting from the platform's curated cipher configuration and can lead to compatibility issues if the static list falls out of date.

## Expected Behavior

- When the underlying SSL library is detected to have trustworthy built-in defaults, the library should skip overriding the cipher list and defer to the platform configuration.
- When the user explicitly specifies ciphers, that explicit list should always be honored regardless of the detected SSL library version.
- When the SSL library is older and its defaults cannot be trusted, the library should continue to apply its own safe default cipher list.

## Why This Matters

This allows system administrators to configure cipher policy once at the OS/SSL library level and have all applications (including those using this HTTP library) respect that configuration, rather than being overridden by hardcoded application-level defaults.
