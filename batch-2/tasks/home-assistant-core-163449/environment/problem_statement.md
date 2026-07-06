## Description

Home Assistant needs a new integration to support Zinvolt smart balcony battery storage systems. Currently, there is no way for users to monitor their Zinvolt battery through Home Assistant. The integration should allow users to authenticate with their Zinvolt account and expose their battery's data as Home Assistant entities.

## Expected Behavior

- Users can add the integration through the standard guided setup UI by entering their email and password.
- On successful authentication, a device representing the user's battery is registered in Home Assistant, identified by the battery's serial number, with the manufacturer name "Zinvolt".
- A sensor entity showing the battery's current state of charge (as a percentage) is created under the device. It should be categorized as diagnostic information, named "Battery" (derived from its device class), and its unique identifier must combine the battery serial number with a fixed sensor-specific suffix.
- The unique identifier for the integration config entry itself must be derived from the authenticated user's identity embedded in the access token.
- During setup, the form should validate credentials and display appropriate error messages for authentication failures, connectivity issues, or unexpected errors — without losing user input.
- If the same account is added a second time, the flow should detect the duplicate and abort gracefully.

## Why This Matters

Zinvolt battery users want to monitor their energy storage from within Home Assistant alongside other home automation data. Without this integration, there is no way to access battery status data through the platform.
