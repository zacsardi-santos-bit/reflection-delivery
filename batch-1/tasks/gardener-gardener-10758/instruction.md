Update the blackbox exporter scrape configuration builder to use plain string keys for label maps. Ensure that all static monitoring target configurations adhere to the updated API requirements. Correct any type mismatches to prevent compilation errors.

*   Modify the Labels field in each StaticConfig object:
    *   Ensure it is typed as map[string]string, replacing any use of a specialized named type.
*   Update all static monitoring target configurations:
    *   For the API server, Kubernetes API server, dashboard, and discovery server.
    *   Use map[string]string for their Labels field.
    *   Set the 'purpose: availability' label with a plain string key.
*   Ensure compatibility with the current monitoring API:
    *   Update the scrape configuration code in `pkg/component/observability/monitoring/blackboxexporter/garden/scrapeconfig.go`.
    *   Verify that StaticConfig structs have a Labels field using the map[string]string type.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.