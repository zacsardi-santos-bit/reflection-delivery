## Description

There is a bug in the runtime configuration reload logic for pinned images. When an administrator updates the configuration to set an empty list of pinned images and triggers a config reload, the runtime does not actually remove the previously pinned images. Instead, the old pinned images remain in place as if the reload never happened.

## Expected Behavior

- When the pinned images list is set to empty in the configuration and a reload is triggered, all previously pinned images should be unpinned.
- A configuration reload with an empty pinned images list should result in zero pinned images being tracked by the runtime.

## Current Behavior

After reloading a configuration that specifies no pinned images, the runtime continues to treat the old images as pinned. The only workaround is to fully restart the runtime service.

## Why This Matters

Operators need the ability to manage the set of pinned images dynamically through configuration reloads without restarting the runtime. The inability to clear all pinned images via reload is a significant operational limitation, as it forces unnecessary service restarts just to remove image pinning.
