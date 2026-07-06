Refactor the zone selection logic for restoring or cloning block storage volumes across cloud regions. Update the Kubernetes client handling and make the zone filtering utility publicly accessible. Implement error handling for specific cases where no valid zones or no provider zones are available.

*   Update the `SanitizeAvailableZones` function:
    *   Export this function in `pkg/blockstorage/zone/zone.go`.
    *   Accept a map of candidate zone names and a slice of valid zone names.
    *   Return a map of zones present in the valid list, mapping unrecognized zones to their closest match.

*   Modify the `FromSourceRegionZone` function:
    *   Change its signature to accept a `kubernetes.Interface` client as the third parameter.
    *   Ensure it returns an error with the message containing 'Unable to find valid availabilty zones for region' when no valid zones are found.
    *   Ensure it returns an error with the message containing 'No provider zones for region' when the region has no availability zones or the Mapper returns an error.
    *   Return at least one valid zone when source zones exist in the target region as reported by Kubernetes node topology.
    *   Implement fallback to consistent zone selection from available Kubernetes node zones when source zones are not directly found.
    *   Implement fallback to validate source zones against the Mapper's static zone list when Kubernetes reports no zones for the region.

*   Update the `Mapper` interface:
    *   Define a `FromRegion(ctx context.Context, region string) ([]string, error)` method to return valid availability zones for a given region.

*   Adjust the `consistentZone` function:
    *   Ensure it has the signature `consistentZone(sourceZone string, availableZones map[string]struct{}) string`.
    *   Return an empty string when `availableZones` is empty.
    *   Ensure it returns the same zone for the same `sourceZone` and set of available zones, regardless of map iteration order.
    *   Ensure it returns a different zone when the set of available zones changes.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.