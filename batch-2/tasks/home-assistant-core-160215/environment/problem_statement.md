# Add Pet Last-Seen Flap Device and User Sensors for Sure Petcare Integration

## Description

The Sure Petcare integration tracks pets and their positions when they pass through smart flap devices. The API already returns metadata about which specific device a pet was last seen at, and which user last manually recorded the pet's location — but this data is not currently exposed as Home Assistant sensor entities.

## Expected Behavior

- A new diagnostic sensor per pet should report the ID of the flap device the pet was last detected at.
- A new diagnostic sensor per pet should report the ID of the user who last manually recorded the pet's location.
- Both sensors should be **disabled by default** so they don't clutter dashboards for users who don't need them. Users can manually enable them in the entity registry when desired.
- When disabled, these entities should still appear in the entity registry but should not produce any active state.

## Why This Matters

Users who want to build automations based on which specific flap device a pet last used, or who want to track manual pet location updates by household members, currently have no way to access this data through Home Assistant. Exposing these values as sensors (disabled by default) gives power users the option to use this data without affecting the experience for casual users.
