# Add Climate Entity to WaterFurnace Integration

## Description

The WaterFurnace geothermal integration currently only exposes sensor data — power consumption, temperatures, fan speed, and similar read-only values. However, there is no way to actually control the heat pump through Home Assistant's standard climate interface. Users who want to set heating or cooling mode, adjust temperature targets, or change humidity settings have to use a separate app, even though the underlying library already supports these control operations.

## Expected Behavior

The integration should provide a climate entity that:

- Reports the current operating mode (heating, cooling, automatic heat-cool, or off)
- Shows the current HVAC action (e.g., actively heating, cooling, fan running, idle, or locked out)
- Allows setting the operating mode to off, heat, cool, or automatic (heat-cool)
- Supports setting a single temperature target in heat or cool mode, and a low/high temperature range in automatic mode
- Supports setting a target humidity level
- Raises a clear error to the user when the geothermal unit cannot be reached or returns a failure during a control operation, rather than silently ignoring it

In addition, some sensor readings (for heating setpoint, cooling setpoint, and dehumidification setpoint) that previously showed as unknown should now correctly report their values when the device provides that data.

## Why This Matters

Without a climate entity, users must leave Home Assistant to control their geothermal system, breaking the unified home automation experience. Exposing the thermostat through the standard climate interface lets users integrate it with automations, dashboards, and voice assistants just like any other thermostat.
