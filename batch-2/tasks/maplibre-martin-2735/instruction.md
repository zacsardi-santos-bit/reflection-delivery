I'm working on a tile server dashboard and I'd like to add cache hit-rate statistics to the analytics view.

*   The parseCacheMetrics function must parse Prometheus text-format lines for 'martin_cache_requests_total' (zoom-less caches such as font and sprite) and 'martin_tile_cache_requests_total' (per-zoom caches such as tile and pmtiles_directory), returning a Record<string, CacheMetrics> keyed by cache name.

*   Each CacheMetrics value must have 'hits' and 'misses' as total numeric counts. For zoom-less caches, 'byZoom' must be an empty array. For per-zoom caches, 'byZoom' must be an array of objects each containing 'hits', 'misses', and 'zoom' (numeric), sorted ascending by zoom level.

*   parseCacheMetrics must sum counts when the same cache/result/zoom label combination appears more than once (e.g., from multiple workers reporting the same labels).

*   parseCacheMetrics must return an empty object for empty input. Cache names that do not appear in the input must be omitted from the output.

*   parseCacheMetrics must silently ignore lines that are not one of the two recognized metric names, lines whose values are not valid finite numbers, and result label values other than 'hit' or 'miss'.

*   The hitRate function must accept an object with 'hits' and 'misses' numeric fields and return hits / (hits + misses) as a number when the total is greater than zero, or null when both hits and misses are zero.

*   The AnalyticsData type must include a 'caches' field of type Record<string, CacheMetrics> where CacheMetrics is imported from the prometheus module.

*   The AnalyticsSection component must display a cache hit-rate row for each cache key present in analytics.caches, using these exact display labels: 'tile' → 'Tile cache', 'pmtiles_directory' → 'PMTiles dirs', 'font' → 'Font cache', 'sprite' → 'Sprite cache'.

*   Hit rates must be displayed as 'XX.X% hit' (one decimal place percentage). When a cache has zero total requests (hits and misses both 0), 'no requests yet' must be displayed instead.

*   A zoom-breakdown trigger button must be rendered with aria-label '<label> hit rate by zoom' (where <label> is the display label above) only for caches whose byZoom array is non-empty. Caches with an empty byZoom array must not have this button.

*   Cache rows must be hidden when the corresponding cache key is absent from analytics.caches (i.e., when analytics.caches is an empty object, no cache rows should be rendered).


*   Interface details: Type: Interface
Name: HitCount
Location: martin/martin-ui/src/lib/prometheus.ts
Description: Represents raw hit and miss counts for a cache.
Signature: { hits: number; misses: number }

Type: Interface
Name: ZoomHitCount
Location: martin/martin-ui/src/lib/prometheus.ts
Description: Extends HitCount with a zoom level, used for per-zoom cache breakdowns.
Signature: { hits: number; misses: number; zoom: number }

Type: Interface
Name: CacheMetrics
Location: martin/martin-ui/src/lib/prometheus.ts
Description: Full cache statistics including aggregated totals and an optional per-zoom breakdown. byZoom is an empty array for caches without a zoom dimension (e.g., font, sprite).
Signature: { hits: number; misses: number; byZoom: ZoomHitCount[] }

Type: Function
Name: hitRate
Location: martin/martin-ui/src/lib/prometheus.ts
Signature: hitRate(counts: HitCount): number | null
Description: Returns the hit rate as a number in [0, 1] (hits / (hits + misses)), or null when both hits and misses are zero.

Type: Function
Name: parseCacheMetrics
Location: martin/martin-ui/src/lib/prometheus.ts
Signature: parseCacheMetrics(text: string): Record<string, CacheMetrics>
Description: Parses a Prometheus text-format string and returns a Record keyed by cache name. Reads two metric families:
  - martin_cache_requests_total{cache="<name>",result="hit|miss"} <count> — zoom-less caches (e.g. font, sprite); byZoom will be [].
  - martin_tile_cache_requests_total{cache="<name>",result="hit|miss",zoom="<n>"} <count> — per-zoom caches (e.g. tile, pmtiles_directory); byZoom entries are sorted ascending by zoom.
Duplicated label combinations are summed (e.g. across workers). Only cache names that appear in the input are returned. Unrelated metric names, malformed lines, non-numeric values, and result labels other than "hit" or "miss" are silently ignored. Returns {} for empty input.

Type: Field
Name: caches
Location: martin/martin-ui/src/lib/types.ts
Description: Field added to the AnalyticsData interface. Holds per-cache metrics returned by parseCacheMetrics.
Signature: caches: Record<string, CacheMetrics>

Type: Component
Name: AnalyticsSection
Location: martin/martin-ui/src/components/analytics-section.tsx
Description: Renders cache hit-rate rows within each analytics card when cache data is present. Uses the following exact cache-key-to-display-label mappings:
  - "tile"               → "Tile cache"
  - "pmtiles_directory"  → "PMTiles dirs"
  - "font"               → "Font cache"
  - "sprite"             → "Sprite cache"
Hit rates are formatted as "XX.X% hit" (one decimal, percentage). When both hits and misses are 0 the label "no requests yet" is displayed instead. A zoom-breakdown button is rendered with aria-label "<label> hit rate by zoom" only for caches whose byZoom array is non-empty. Cache rows are hidden when the cache key is absent from analytics.caches.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.