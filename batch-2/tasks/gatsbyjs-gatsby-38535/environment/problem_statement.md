## Description

The development error overlay crashes when it encounters runtime errors that don't have a stack trace. Some errors — particularly those thrown in certain environments or manually constructed errors — may not include stack information, and the overlay currently fails entirely in these cases instead of showing the user the error message.

## Expected Behavior

- When the error overlay receives errors that include a stack trace, it should display the error message visibly as it does today.
- When the error overlay receives errors that do **not** have a stack trace, it should still display the error message visibly rather than crashing.
- Duplicate errors passed to the overlay should be deduplicated so each unique error appears only once.

## Current Behavior

If any error in the errors list lacks a stack trace, the overlay throws an exception instead of rendering. The developer sees nothing useful — just a broken overlay.

## Why This Matters

Developers rely on this overlay during development to understand what went wrong. When the overlay itself crashes due to a missing stack trace, it defeats its own purpose. The overlay should degrade gracefully and always show at minimum the error message, even when detailed stack information is unavailable.
