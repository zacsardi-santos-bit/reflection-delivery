## Description

The Zigbee2mqtt integration needs a way to fully reset itself to a clean state. Currently, if a user's configuration becomes corrupted or they need to switch to a different Zigbee coordinator, there is no built-in mechanism to wipe everything and start fresh. Users are stuck manually cleaning up files and settings.

In addition, the Docker container operations for stopping and removing containers are too strict: if you try to stop a container that is already stopped, or remove a container that no longer exists, the system throws an error even though these are effectively no-ops. This causes unnecessary failures during cleanup sequences.

## Expected Behavior

- A new "reset" action should be available for the Zigbee2mqtt integration that:
  - Stops and removes all related Docker containers
  - Deletes all configuration stored in the database for the integration
  - Removes the Zigbee2mqtt data folder from disk
  - Clears all in-memory runtime state back to initial values
  - Emits a status update so the UI reflects the clean state
  - Propagates the error if the disk cleanup fails
  - Also handles cleanup of an active MQTT connection and any scheduled background jobs

- The container stop operation should silently succeed when Docker reports the container is already stopped (HTTP 304)
- The container remove operation should silently succeed when Docker reports the container does not exist (HTTP 404)
- Other Docker errors during stop or remove should still be propagated as failures

## Why This Matters

Without a reset option, users with a broken Zigbee2mqtt setup have no clean recovery path. The overly strict container operations also cause intermittent failures during integration teardown, which makes the system feel fragile.
