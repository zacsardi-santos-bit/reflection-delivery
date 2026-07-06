## Description

There are two related issues with how kanidm handles POSIX numeric identifiers for groups and service accounts:

1. **Missing group attribute purge in the client library.** There is currently no way, through the client library, to remove a specific attribute from a group. In particular, administrators have no way to clear a group's automatically-assigned numeric identifier so that it can be regenerated. This capability would be useful when migrating to a new ID allocation scheme or resolving conflicts.

2. **Incorrect minimum ID threshold for manual assignments.** When an administrator manually specifies a numeric user or group identifier, the validation rejects values that should be valid. The standard convention on Linux is that system-reserved identifiers occupy the range below 1000, while regular user accounts start at 1000. The current validation is too strict and rejects values that fall within the valid regular-user range (1000 and above).

## Expected Behavior

- The client library should expose a method to delete a specific attribute from a group, allowing administrators to remove and reset the group's numeric identifier.
- Manually specified numeric identifiers of 1000 and above should be accepted as valid.
- Manually specified numeric identifiers below 1000 should be rejected, since that range is reserved by the operating system.

## Why This Matters

Without the ability to purge group attributes through the client, workflows that require resetting or migrating GID values are impossible to perform programmatically. The incorrect validation threshold also blocks legitimate administrative configurations where identifiers in the standard user range (starting at 1000) need to be assigned manually.
