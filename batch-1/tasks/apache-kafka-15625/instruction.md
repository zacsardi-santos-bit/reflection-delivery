Implement quota management for Kafka's Remote Log Manager to limit bandwidth usage during log segment copy and fetch operations. Introduce byte-rate quotas with configurable limits and rolling time windows for both operations. Ensure quotas can be updated at runtime without affecting other metrics.

*   Create `RLMQuotaManagerConfig` class:
    *   Constructor: `RLMQuotaManagerConfig(long quotaBytesPerSecond, int numQuotaSamples, int quotaWindowSizeSeconds)`
    *   Default values: `quotaBytesPerSecond=Long.MAX_VALUE`, `numQuotaSamples=61` (copy), `numQuotaSamples=11` (fetch), `quotaWindowSizeSeconds=1`
    *   Getter methods: `long quotaBytesPerSecond()`, `int numQuotaSamples()`, `int quotaWindowSizeSeconds()`

*   Develop `RLMQuotaManager` class:
    *   Constructor: `RLMQuotaManager(RLMQuotaManagerConfig config, Metrics metrics, QuotaType quotaType, String description, Time time)`
    *   Methods:
        *   `boolean isQuotaExceeded()`: Returns false if no bytes recorded, true if rate exceeds quota, false again when window elapses.
        *   `void record(double bytes)`: Records bytes for quota accounting.
        *   `void updateQuota(Quota quota)`: Updates quota, affecting only byte-rate metrics matching the quota type.

*   Update `RemoteLogManager` class:
    *   Constructor: Accept `Metrics` instance as an additional parameter.
    *   Static methods:
        *   `RLMQuotaManagerConfig copyQuotaManagerConfig(RemoteLogManagerConfig rlmConfig)`: Reads copy quota settings from config.
        *   `RLMQuotaManagerConfig fetchQuotaManagerConfig(RemoteLogManagerConfig rlmConfig)`: Reads fetch quota settings from config.

*   Extend `RemoteLogManagerConfig` class:
    *   Visible-for-testing constructor: Add parameters for copy and fetch quotas.
    *   New getter methods for quota properties.
    *   Public static String constants for configuration keys.

*   Add `QuotaType` values in `QuotaFactory.scala`:
    *   `case object RLMCopy extends QuotaType`
    *   `case object RLMFetch extends QuotaType`

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.