## Description

Users in different regions use different measurement systems. When viewing sensor data on the dashboard — distances, speeds, or electric vehicle efficiency metrics — the values are displayed in their original unit without any regard for the user's preferred measurement system. A user who prefers US/imperial units sees metric values (kilometers, meters per second, kilowatt-hours per 100 km), and a user who prefers metric units sees US/imperial values (miles, feet per second, miles per kilowatt-hour). There is no utility in the server-side codebase to handle automatic conversion between these unit systems.

## Expected Behavior

- A unit conversion utility should accept a numeric measurement value, its current unit, and the user's measurement system preference, then return the converted value paired with the appropriate target unit.
- Conversions should work in both directions: metric to US/imperial (for users with a US preference) and US/imperial to metric (for users with a metric preference).
- The supported unit categories include distances (millimeters, centimeters, meters, kilometers, inches, feet, miles), speeds (km/h, m/s, mph, ft/s), and electric vehicle energy efficiency metrics (km/kWh, mi/kWh, kWh/100km, kWh/100mi, Wh/km, Wh/mi).
- When no sensor reading is available, the function should still return the correct target unit while keeping the value absent.
- When the preference is unrecognized, or when no conversion exists for a given unit, the function should return the original value and unit unchanged.
- A smart rounding utility should also be provided that adjusts numeric precision based on the magnitude of the value: keeping full precision for small values, rounding to fewer decimal places for larger values, and returning integers for very large values.

## Why This Matters

Without this utility, displaying measurements to users in their preferred unit system requires ad hoc conversions scattered across the codebase or is simply not done at all. Centralizing this logic in a shared utility makes it easy to apply consistent, well-tested unit conversion throughout the application.
