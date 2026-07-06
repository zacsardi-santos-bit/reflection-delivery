I'm working on cleaning up how we control monitoring behavior across our error classes. Right now we have a boolean flag called something like "should log as info" spread across different error constructors, but the naming is inconsistent and it only supports two states — it doesn't cleanly express "ignore this completely" versus "log it as info" versus "log it as a real error."

I'd like to replace these boolean flags with a single named enumeration that has at least three distinct values — one for regular error capture, one for info-level capture, and one for suppressing the event entirely. All the error classes that currently accept the boolean flag should be updated to use this new type instead.

Additionally, I need a new hook that reads an existing remote feature flag that controls logging verbosity and translates it into the appropriate value from this new enumeration, so callers don't have to do that translation themselves. The function that retrieves email update status also currently takes the boolean flag directly and should be updated to accept the new type.

The goal is a consistent, expressive API for controlling how errors are reported across the whole monitoring system.
