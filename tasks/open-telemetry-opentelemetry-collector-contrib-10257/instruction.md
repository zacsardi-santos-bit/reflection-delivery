Update the Dynatrace exporter for the OpenTelemetry Collector to correctly estimate the minimum and maximum values from histogram bucket distributions. Ensure that the estimated range always includes the average of the data.

*   Implement logic to adjust the min and max estimates:
    *   If the computed average (sum ÷ count) is greater than the estimated maximum, set the maximum to the average.
    *   If the computed average is less than the estimated minimum, set the minimum to the average.
*   Ensure the following test cases are satisfied:
    *   For a histogram with explicit bounds [0, 10], bucket counts [0, 0, 2], count=2, and sum=30:
        *   The serialized output must include min=10, max=15, sum=30, count=2.
    *   For a histogram with explicit bounds [10, 20], bucket counts [2, 0, 0], count=2, and sum=10:
        *   The serialized output must include min=5, max=10, sum=10, count=2.
*   Maintain the serialized output format for delta histograms as:
    *   '{prefix}.{name},{dimensions} gauge,min={min},max={max},sum={sum},count={count} {timestamp_milliseconds}'.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.