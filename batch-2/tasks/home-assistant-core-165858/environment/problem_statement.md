## Description

The Growatt Server integration is using a non-standard approach to control the number of decimal places displayed for sensor readings. Currently, the integration defines a custom field on its sensor description objects and applies rounding directly to raw numeric values before reporting them. This design causes precision loss in the underlying data and does not align with the platform's built-in mechanism for controlling display precision.

The correct approach is to use the platform's standard "suggested display precision" feature, which tells the display layer how many decimal places to show without modifying the raw data. This preserves full data granularity while still presenting values in a user-friendly format.

## Expected Behavior

- The custom precision field and manual rounding logic should be removed from the sensor implementation
- The platform-standard suggested display precision mechanism should be used instead across all Growatt sensor types
- Most measurement sensors (energy, power, current, voltage, frequency) should suggest 1 decimal place for inverter, TLX, and min device types
- Storage-specific measurements such as battery voltage, AC voltages, and AC frequencies should suggest 2 decimal places, reflecting the higher precision those sensors provide
- The "load percentage" sensor in the storage device type should also have a suggested display precision of 2 decimal places
- One exception: the first voltage input sensor in the inverter device type should suggest 2 decimal places rather than 1

## Why This Matters

Using the standard display precision mechanism instead of manual rounding improves data integrity (full-precision values are stored and available for automations/history), brings the integration in line with Home Assistant quality guidelines, and ensures consistent behavior with the rest of the platform's sensor ecosystem.
