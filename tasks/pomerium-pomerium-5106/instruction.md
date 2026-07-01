Implement a resource monitor for Pomerium that detects memory pressure in containerized environments and communicates this to the proxy data plane. This monitor should read memory usage and limits from the cgroup filesystem, compute a memory saturation ratio, and write it to a file. It must support cgroup v1, v2, and hybrid configurations, and allow disabling via a runtime flag.

*   Define `CgroupFilePath` type and constants `RootPath`, `MemoryUsagePath`, and `MemoryLimitPath` in the `pkg/envoy` package for Linux-only builds.

*   Implement the `CgroupDriver` interface with methods:
    *   `Path(name string, path CgroupFilePath) string`
    *   `CgroupForPid(pid int) (string, error)`
    *   `MemoryUsage(name string) (uint64, error)`
    *   `MemoryLimit(name string) (uint64, error)`
    *   `Validate(name string) error`

*   Implement `cgroupV2Driver` and `cgroupV1Driver` structs to satisfy `CgroupDriver`:
    *   `cgroupV2Driver` should handle paths like `{root}/{name}/memory.current` and `{root}/{name}/memory.max`.
    *   `cgroupV1Driver` should handle paths like `{root}/memory/{name}/memory.usage_in_bytes` and `{root}/memory/{name}/memory.limit_in_bytes`.

*   Implement `findMountpoint(fsys fs.FS) (string, bool, error)` to determine the cgroup mountpoint.

*   Implement `NewSharedResourceMonitor` to create a resource monitor, initializing a file at `{dataDir}/resource_monitor/memory/cgroup_memory_saturation` with "0".

*   Implement `ResourceMonitor` interface with methods:
    *   `Run(ctx context.Context, envoyPid int) error` to compute and write memory saturation.
    *   `ApplyBootstrapConfig(bootstrap *envoy_config_bootstrap_v3.Bootstrap)` to configure overload actions based on saturation thresholds.

*   Implement `computeScaledTickInterval(saturation float64) time.Duration` to adjust polling intervals based on memory saturation.

*   Define package-level variables for tick intervals:
    *   `monitorInitialTickDelay` (default 1s)
    *   `monitorMaxTickInterval` (default 10s)
    *   `monitorMinTickInterval` (default 250ms)

*   Define the runtime flag `RuntimeFlagEnvoyResourceManagerEnabled` in `config/runtime_flags.go` with a default value of true.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.