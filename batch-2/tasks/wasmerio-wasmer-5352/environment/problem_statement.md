## Description

The cron expression type in the Wasmer configuration library parses and stores cron schedules, but the internal parsed representation is not accessible to callers. This means it is impossible to obtain a human-readable description of what a cron schedule actually does — for instance, understanding that a certain expression means "Every hour."

## Expected Behavior

- Cron expressions should expose their underlying parsed schedule object as a public field, so that callers can programmatically work with it.
- Given a cron expression like "@hourly", it should be possible to retrieve a human-readable English description such as "Every hour" by calling the appropriate describe method on the exposed field.

## Why This Matters

User interfaces, logging systems, and developer tooling often need to present cron schedules in plain language rather than as raw expressions. Without exposing the internal schedule object, this kind of human-readable output is not achievable. Exposing the underlying cron value allows downstream code to generate friendly, localized descriptions of scheduled jobs.
