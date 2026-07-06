## Description

The Kafka Streams group coordinator does not currently track whether a topology description plugin has been successfully run and stored for a given group's current topology version, nor whether the description attempt failed. When the coordinator restarts, there is no way to determine whether an existing stored description is still current or needs refreshing, which can lead to unnecessary repeated description work or missed refreshes.

## Expected Behavior

- Each streams group should persistently track two epoch values in its metadata record:
  - The last topology epoch for which a description was successfully stored
  - The last topology epoch for which description failed
- These values must survive coordinator restarts (they must be persisted in the metadata record and restored on replay).
- The group describe operation should return these per-group epoch values alongside the list of described groups, so the service layer can decide whether to trigger new description work.
- A new validation operation should allow callers to check whether a specific member belongs to a streams group at a given committed state, returning an appropriate error if the group does not exist or the member is not a current member of the group. This operation must not be affected by uncommitted records.

## Why This Matters

Without these epoch fields, the coordinator cannot efficiently manage topology descriptions across restarts or between heartbeats. The per-group epoch values give the service layer enough information to avoid redundant description work and to detect when description needs to be retried. The member-validation operation is a prerequisite for an upcoming request handler that processes topology description updates submitted by a group member.
