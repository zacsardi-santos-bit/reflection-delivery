## Description

The dataset viewer statistics endpoint currently supports numeric, string, categorical, boolean, list, audio, and image columns — but has no support for datetime or timestamp columns. When a dataset contains date/time data, no statistics are computed for those columns. Additionally, string columns that store dates as text are treated as generic text and analyzed purely by string length, missing an opportunity to provide meaningful temporal insights.

## Expected Behavior

- Timestamp/datetime columns should have dedicated statistics computed: minimum, maximum, mean, median, and standard deviation of the datetime values, along with a histogram showing the distribution of timestamps over time.
- All datetime statistics values (min, max, mean, median) should be returned as human-readable strings with precision up to seconds.
- Standard deviation should be returned as a human-readable time duration string.
- Histograms should use datetime strings (not numeric values) as bin edges.
- For all-null datetime columns, all statistical fields should be null.
- For timezone-aware datetime columns, the timezone offset should be preserved in all output strings.
- String columns that consistently use a single recognizable datetime format should be automatically detected and treated as datetime data, returning the same statistics as native datetime columns (this behavior is controlled by a feature flag).
- String columns with mixed or unrecognizable datetime formats should fall back to regular string statistics.

## Why This Matters

Datetime data is extremely common in real-world datasets (event logs, medical records, financial transactions, etc.). Without support for temporal statistics, users cannot understand the time range covered by a dataset, the typical or average date, or how values are distributed over time. This makes the statistics feature significantly less useful for any time-series or time-stamped dataset.
