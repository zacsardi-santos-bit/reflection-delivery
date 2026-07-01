## Description

The Dynatrace exporter estimates minimum and maximum values from histogram bucket distributions when exporting metrics. The current estimation logic finds the lowest non-empty bucket boundary as the min, and the highest non-empty bucket boundary as the max. However, this produces incorrect results when data concentrates in the "overflow" bucket (above the highest explicit boundary) or the "underflow" bucket (below the lowest explicit boundary).

## Problem

When most or all data points fall into the last bucket (beyond the highest boundary), the estimated maximum is capped at the highest boundary — even though the actual average of the data clearly exceeds it. Similarly, when all data points are in the first bucket (below the lowest boundary), the estimated minimum is the lowest boundary, which is higher than the actual average.

This creates an inconsistency where the reported range (min to max) does not contain the average, which is mathematically impossible for a valid data set.

## Expected Behavior

- When the estimated maximum is less than the average (sum ÷ count), the maximum should be updated to equal the average.
- When the estimated minimum is greater than the average, the minimum should be updated to equal the average.
- This ensures that the reported min/max range always contains the mean, maintaining internal consistency of exported histogram metrics.

## Why This Matters

Metrics consumers rely on accurate min/max estimates to understand the distribution of their data. An estimated range that excludes the average is misleading and can cause confusion or incorrect alerting downstream.
