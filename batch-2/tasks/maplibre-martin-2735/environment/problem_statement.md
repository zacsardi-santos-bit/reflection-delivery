## Description

The martin tile server analytics dashboard shows request throughput and latency data, but provides no visibility into how well the server's internal caches are performing. Operators and developers have no way to see cache hit rates from the UI, making it impossible to evaluate whether caching is effective or whether configuration adjustments are needed.

## Expected Behavior

- The analytics view should display cache hit rates for each type of cache the server uses (tile cache, PMTiles directory cache, font cache, and sprite cache).
- Hit rates should be shown as a percentage. If a cache has not yet received any requests, the UI should clearly indicate that rather than showing a zero percent rate.
- For caches that track data by zoom level (tile and PMTiles directory caches), an additional breakdown should be available showing per-zoom hit rates.
- Cache rows should only appear when the server has reported data for that cache; if no cache data is available, the section should be hidden.

## Why This Matters

Without cache hit rate visibility, operators cannot tell whether caches are working correctly, whether cache sizes need adjustment, or whether particular zoom levels are causing poor cache utilization. Adding this information to the existing analytics dashboard gives operators actionable insight into server performance without requiring them to manually parse raw metrics output.
