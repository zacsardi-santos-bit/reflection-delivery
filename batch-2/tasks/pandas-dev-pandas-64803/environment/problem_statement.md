## Description

There is a bug in datetime index intersection where two ranges that share the same frequency but do not lie on the same "grid" can produce silently incorrect results. The optimization that speeds up intersection checks for a matching frequency but does not verify that the two ranges are actually aligned, causing the fast path to be used incorrectly.

## Affected Scenarios

- Two datetime ranges with the same business-day (or other variable-stride) frequency but different times of day — for example, one range starting at 09:00 and another at 10:00 — will never share a timestamp, yet the fast path may incorrectly identify overlapping elements.
- Offset types with variable or calendar-dependent spacing (no fixed stride) cannot guarantee alignment but may still enter the fast path.
- When the frequency step size is greater than 1 (e.g., "every 2 months"), two ranges can be shifted by a non-multiple of the step and share no elements, yet the optimizer treats them as aligned.
- Weekly ranges that use an unanchored weekday can produce different grids depending on the start date, causing misaligned ranges to appear compatible.

## Expected Behavior

- The intersection of two non-aligned datetime ranges should return the correct (often empty) result.
- The fast intersection optimization should only be applied when both ranges are verifiably on the same grid, taking into account: wall-clock time of day, weekday alignment for unanchored weekly offsets, and whether the frequency has a fixed stride with a multiplier of exactly 1.
- Correct results must also be produced for multipliers greater than 1 or negative multipliers, even though those cases skip the fast path.

## Why This Matters

Users who intersect date ranges with matching frequencies but different time-of-day starts, or who use compound offsets and multi-step frequencies, can get silently wrong data. Since no error is raised, these incorrect results may propagate undetected into downstream calculations.
