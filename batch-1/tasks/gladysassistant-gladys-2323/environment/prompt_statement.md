I'm working on a home automation platform that displays sensor readings to users. The issue is that measurements are always shown in their original unit regardless of the user's preferred measurement system. Someone who prefers US units sees values in kilometers, meters, and metric energy efficiency units, while someone who prefers metric units sees values in miles and feet.

I need a shared utility function in the server-side codebase that takes a measurement value, its current unit, and the user's measurement preference, and returns the converted value along with the appropriate unit for that preference. It should handle distances (like millimeters, centimeters, meters, kilometers, inches, feet, and miles), speeds (like km/h, m/s, mph, and ft/s), and electric vehicle range and consumption metrics in both unit systems. The conversion should work bidirectionally — metric to US and US to metric.

The function should handle edge cases gracefully: if there is no sensor reading yet, it should still return the correct target unit while keeping the value absent. If the preference is unknown or no conversion exists for the given unit, it should return the original value and unit unchanged.

I also need a companion utility that rounds numbers intelligently based on their magnitude — keeping full precision for very small values, two decimal places for small numbers, one decimal place for medium-sized numbers, and no decimal places for large numbers. This rounding should be applied to converted values automatically.

Both utilities should be exported from the existing units file at server/utils/units.js.
