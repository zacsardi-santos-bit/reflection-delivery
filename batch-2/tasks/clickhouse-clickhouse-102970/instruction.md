I've found a bug in ClickHouse's best-effort datetime parsing when parsing datetime strings that include timezone offsets.

*   When date_time_input_format is set to 'best_effort' and a datetime string with a timezone offset is parsed via the JSONEachRow format, a range check must be performed on the UTC-adjusted timestamp after the timezone offset is applied.

*   If the UTC-adjusted timestamp exceeds the maximum value representable by the 32-bit DateTime type (UINT32_MAX seconds), the parser must fall back to DateTime64 rather than wrapping around; the inferred type must be Nullable(DateTime64(9)) and the value must reflect the correct UTC time (e.g., '2106-02-07 06:28:15-01:00' produces type Nullable(DateTime64(9)) and value '2106-02-07 07:28:15.000000000').

*   If the UTC-adjusted timestamp is negative (before the Unix epoch), the parser must fall back to DateTime64 rather than clamping; the inferred type must be Nullable(DateTime64(9)) and the value must reflect the correct UTC time (e.g., '1970-01-01 00:00:00+01:00' produces type Nullable(DateTime64(9)) and value '1969-12-31 23:00:00.000000000').

*   Datetime strings whose UTC-adjusted timestamps remain within the valid DateTime range (0 to UINT32_MAX inclusive) must still be inferred as Nullable(DateTime) (e.g., '2106-02-07 06:28:15+01:00' produces type Nullable(DateTime) and value '2106-02-07 05:28:15', and '1970-01-01 01:00:00+01:00' produces type Nullable(DateTime) and value '1970-01-01 00:00:00').

*   The range check must be applied only in the non-64-bit datetime parsing path inside parseDateTimeBestEffortImpl in src/IO/parseDateTimeBestEffort.cpp, immediately after the timezone offset adjustment is applied.


*   Interface details: Type: Function
Name: parseDateTimeBestEffortImpl
Location: src/IO/parseDateTimeBestEffort.cpp
Signature: parseDateTimeBestEffortImpl<is_64, ...>(...)
Description: Template function that parses a datetime string in best-effort mode. When the template parameter is_64 is false (i.e., parsing into a 32-bit DateTime), a range check must be added immediately after the timezone adjustment (adjust_time_zone()) is applied. If the adjusted result (res) is less than 0 or greater than UINT32_MAX, the function must return false to signal that DateTime parsing failed and trigger fallback to DateTime64 parsing.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.