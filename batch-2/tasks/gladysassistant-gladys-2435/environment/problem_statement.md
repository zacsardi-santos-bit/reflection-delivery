## Description

The Zigbee2MQTT integration needs several improvements to better support modern Zigbee hardware and help users diagnose startup problems.

First, a new adapter protocol type should be added alongside the existing ones. Several popular Zigbee USB dongles that previously only worked under a legacy protocol can now use a newer, preferred driver. These devices should appear in the adapter list twice — once for the new preferred mode and once for the legacy compatibility mode (clearly labeled as such). The adapter list itself should now include metadata about which protocol type each entry uses, not just a human-readable name.

Second, the system needs to detect when the user has switched from one adapter protocol type to another. When this happens, simply restarting the container is not enough — the container must be fully removed and recreated to ensure a clean state. The function that configures the container should therefore return more detailed information about what changed (configuration content vs. adapter type), so callers can make the right decision.

Third, after the container starts, the system should read its logs to detect known error conditions. In particular, if the adapter firmware is incompatible with the host software, this should be surfaced as a specific, identifiable error. Other unrecognized error lines should also be captured and reported. If no error is found, that should be reflected too.

Finally, when the bridge reports information about the coordinator hardware, the firmware version details should be extracted and included in the integration's status so they can be displayed to the user.

## Expected Behavior

- Adapter list entries should carry both a display label and a protocol type identifier
- Adapters supporting both new and legacy modes should appear as separate entries
- Container configuration should report both whether content changed and whether the adapter type changed
- When the adapter type changes, the container is removed and recreated
- Container logs are read after startup to detect known error codes or generic error messages
- Coordinator firmware version is tracked and included in status updates

## Why This Matters

Users whose Zigbee dongles support a newer protocol should be able to take advantage of it, and when they switch adapter modes, the transition should work correctly without leaving stale state. Error reporting from the container helps users understand why their Zigbee integration failed to start, instead of leaving them to check logs manually.
