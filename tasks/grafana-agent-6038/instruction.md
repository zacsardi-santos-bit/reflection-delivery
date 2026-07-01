Implement support for HTTP-based service discovery in the Prometheus-to-Flow configuration converter. Ensure the converter can validate and convert Prometheus HTTP service discovery configurations into Flow format, preserving all relevant settings and connections.

*   Implement the `ValidateDiscoveryHttp` function in `converter/internal/prometheusconvert/component/http.go`.
    *   Accept a pointer to a `prom_http.SDConfig` from `github.com/prometheus/prometheus/discovery/http`.
    *   Return a `diag.Diagnostics` slice, which should be empty for valid configurations.

*   Update the converter to support `http_sd_configs` within `scrape_configs`.
    *   Convert Prometheus YAML scrape configs with HTTP service discovery entries into valid Flow `discovery.http` River blocks.
    *   Preserve the URL, refresh interval, and authorization settings in the conversion.
    *   Correctly connect discovery results to associated scrape and remote write blocks.
    *   Maintain job name, metrics path, relabel rules, and remote write targets.

*   Implement the `appendDiscoveryHttp` function in `converter/internal/prometheusconvert/component/http.go`.
    *   Convert a Prometheus HTTP service discovery config into a Flow `discovery.http` block.
    *   Append the block to `PrometheusBlocks` and return the discovery exports (targets reference).

*   Implement the `toDiscoveryHttp` function in `converter/internal/prometheusconvert/component/http.go`.
    *   Convert a Prometheus HTTP SD config into the `Arguments` struct for the Flow `discovery.http` component.
    *   Return `nil` for a `nil` input.

*   Update service discovery dispatch logic in `converter/internal/prometheusconvert/component/service_discovery.go`.
    *   Ensure `AppendServiceDiscoveryConfig` and `ValidateServiceDiscoveryConfig` handle the `*prom_http.SDConfig` type.
    *   Delegate to `appendDiscoveryHttp` and `ValidateDiscoveryHttp` respectively.

*   Ensure the test system uses the `converter/internal/prometheusconvert/testdata/http.yaml` input file and verifies against the `converter/internal/prometheusconvert/testdata/http.river` expected output file.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.