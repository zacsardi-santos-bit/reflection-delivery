## Description

The documentation publishing pipeline for provider releases lacks an intelligent way to determine which individual providers actually had new releases in a given wave. Currently, when the pipeline is triggered with a broad "all providers" token, it cannot distinguish between a full rebuild scenario and an incremental one — it has no mechanism to figure out that only a subset of providers had actual new final releases in that wave.

## Expected Behavior

- When a documentation publish run is triggered for a wave release tag (a date-stamped tag following a standardized date-based naming convention), the pipeline should automatically determine which providers had new final (non-release-candidate) releases since the previous wave tag, and scope the registry update to only those providers.
- When the trigger reference is not a valid wave tag (e.g. a branch name, a commit SHA, or an RC-tagged ref), the pipeline should fall back to a full rebuild.
- When there is no predecessor wave tag (e.g. the very first wave), the pipeline should fall back to a full rebuild and emit a warning.
- When no new final provider release tags exist between the two wave tags, the pipeline should fall back to a full rebuild and emit a warning.
- When all provider tags between waves are release candidates, the system should treat this as no new finals and fall back to a full rebuild with a warning.
- Non-provider tokens in the input (such as those representing the Airflow core, helm chart, or Docker stack) must be ignored when building the provider list.
- Full package name tokens must be normalized to short provider IDs. Dots in provider short names must be converted to hyphens. Duplicate provider entries must be deduplicated.

## Why This Matters

Without this logic, every wave dispatch that uses the "all providers" meta-token triggers a full rebuild of the entire provider registry, even when only a handful of providers were actually updated. An incremental approach based on git release tags makes the pipeline significantly more efficient for normal wave releases.
