Implement a utility function to convert measurement values between metric and US/imperial units based on user preference. Additionally, provide a smart rounding function to adjust numeric precision based on value magnitude.

Requirements:

*   Export `checkAndConvertUnit` from `server/utils/units.js`.
    *   Accepts three arguments: `value` (numeric or null), `fromUnit` (string), and `userPreference` (string, either 'us' or 'metric').
    *   Returns an object with `value` (converted or null) and `unit` (converted unit string).
    *   Convert units based on user preference:
        *   'us' preference:
            *   KM to MILE (÷1.60934)
            *   M to FEET (×3.28084)
            *   MM to INCH (÷25.4)
            *   CM to INCH (÷2.54)
            *   KILOMETER_PER_HOUR to MILE_PER_HOUR (÷1.60934)
            *   METER_PER_SECOND to FEET_PER_SECOND (×3.28084)
            *   KM_PER_KILOWATT_HOUR to MILE_PER_KILOWATT_HOUR (÷1.60934)
            *   KILOWATT_HOUR_PER_100_KM to KILOWATT_HOUR_PER_100_MILE (÷1.60934)
            *   WATT_HOUR_PER_KM to WATT_HOUR_PER_MILE (÷1.60934)
        *   'metric' preference:
            *   MILE to KM (×1.60934)
            *   FEET to M (×0.3048)
            *   INCH to MM (×25.4)
            *   MILE_PER_HOUR to KILOMETER_PER_HOUR (×1.60934)
            *   FEET_PER_SECOND to METER_PER_SECOND (×0.3048)
            *   MILE_PER_KILOWATT_HOUR to KM_PER_KILOWATT_HOUR (×1.60934)
            *   KILOWATT_HOUR_PER_100_MILE to KILOWATT_HOUR_PER_100_KM (×1.60934)
            *   WATT_HOUR_PER_MILE to WATT_HOUR_PER_KM (×1.60934)
    *   Use `smartRound` on converted numeric values before returning.
    *   If `value` is null, return `{ value: null, unit: <converted_unit> }`.
    *   If `userPreference` is unrecognized, return original `{ value, unit: fromUnit }`.
    *   If no conversion rule exists, return original `{ value, unit: fromUnit }`.

*   Export `smartRound` from `server/utils/units.js`.
    *   Accepts a single numeric argument.
    *   Return value unchanged if `Math.abs(value) < 1`.
    *   Round to 2 decimal places if `1 <= Math.abs(value) < 10`.
    *   Round to 1 decimal place if `10 <= Math.abs(value) < 1000`.
    *   Round to nearest integer if `Math.abs(value) >= 1000`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.