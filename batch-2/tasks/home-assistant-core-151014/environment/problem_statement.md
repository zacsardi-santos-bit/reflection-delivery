## Description

The Schlage lock integration in Home Assistant supports locking and unlocking doors remotely, but there is no way to manage the PIN access codes stored on the lock from within Home Assistant. Users and automation authors have no way to programmatically add new access codes, remove existing ones, or list which codes are currently programmed on the lock.

## Expected Behavior

The integration should expose three new services for Schlage lock entities:

- **Add PIN code**: Given a unique name and a numeric PIN (between 4 and 8 digits), add a new access code to the lock. The service should reject codes that are not entirely numeric or outside the 4–8 digit length range. It should also silently enforce that the name and PIN value are not already in use on the lock.
- **Delete PIN code**: Given a name, remove the matching access code from the lock. Name matching should be case-insensitive. If no code with the given name exists, the operation should complete silently without error.
- **Get PIN codes**: Return all currently programmed access codes on the lock, including each code's name and numeric value, keyed by the lock's entity identifier.

All three services should respond with clear, structured error information when a hardware or API failure occurs during the operation, so that automations can handle failures gracefully.

## Why This Matters

Many Schlage lock users want to manage access codes through automations — for example, creating a temporary code for a guest and removing it after their stay. Without these services, users are forced to manage codes through the Schlage mobile app, making it impossible to integrate code management into Home Assistant automations.
