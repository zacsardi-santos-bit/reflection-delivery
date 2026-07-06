## Description

There are two bugs in the game room and scheduler management code that prevent certain valid configurations from working correctly.

First, the container definition within a game room spec incorrectly requires ports to be present. This means any container that legitimately has no ports will fail validation even when everything else is correct. Port configuration should be optional.

Second, when patching a scheduler to update a container's name, the name change is silently ignored. The patch logic handles other container fields (image, pull policy, command, environment, resources, ports) but skips the name field entirely, so container renaming via a patch operation has no effect.

## Expected Behavior

- A game room spec containing containers with no ports should pass validation successfully.
- When a patch operation includes a new name for a container, the resulting scheduler spec should reflect that updated name.

## Why This Matters

These bugs make it impossible to manage port-less container configurations and prevent container names from being changed through the patch workflow. Both cases represent valid use cases that should be supported without workarounds.
