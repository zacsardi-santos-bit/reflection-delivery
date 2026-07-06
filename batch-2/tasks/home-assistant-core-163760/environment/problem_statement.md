# Add Segment Cleaning Support to Roborock Vacuum Integration

## Description

Roborock vacuums that support multiple floor maps have named room segments across those maps. Currently, the Home Assistant Roborock integration has no way to expose those room segments or let users clean specific rooms through automations or the UI.

We need to:
1. Add the ability to query all available room segments across all loaded floor maps for a Roborock vacuum entity.
2. Support cleaning specific named areas (which map to one or more room segments) through the standard vacuum service interface.

## Expected Behavior

- A new way to retrieve all room segments across all loaded floor maps, where each segment has a unique identifier (scoped to its floor map), a room name, and a floor group name.
- When no map data is available, an empty list of segments is returned.
- The standard vacuum "clean area" service should work on Roborock entities. When triggered with a named area, the integration resolves the area to its segment IDs and issues a clean command to the robot.
- If the target segments are on a different floor map than the currently active one, the integration automatically switches to the correct map before starting the clean.
- Clear, user-friendly error messages must be provided when:
  - A cleaning request mixes segments from multiple different floor maps.
  - A segment identifier is malformed (missing the map-scope separator or contains non-numeric parts).
  - The attempt to switch to the required floor map fails.

## Why This Matters

Multi-floor Roborock users need to be able to trigger room-specific cleaning through Home Assistant automations. Without this, users cannot target individual rooms on different floor plans, limiting the integration's usefulness for homes with multiple floors.
