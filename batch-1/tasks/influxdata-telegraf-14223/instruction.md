Implement a new general-purpose SNMP tag lookup processor for Telegraf to enrich metrics with SNMP-derived tags. Ensure the processor caches SNMP responses, supports concurrent lookups, and maintains metric order when required. Introduce a shared SNMP client configuration helper to avoid code duplication.

*   Implement a function in the internal SNMP package:
    *   Name: `DefaultClientConfig`
    *   Location: `internal/snmp/config.go`
    *   Returns a `*ClientConfig` with default values: Timeout 5s, Retries 3, Version 2, Community "public", MaxRepetitions 10, SecLevel "authNoPriv", SecName "myuser", AuthProtocol "MD5", AuthPassword "pass", Translator "gosmi".

*   Create a new streaming processor plugin:
    *   Name: `snmp_lookup`
    *   Register in the processors registry with defaults: AgentTag 'source', IndexTag 'index', CacheSize 100, CacheTTL 8 hours, ParallelLookups 16.

*   Define constants in `plugins/processors/snmp_lookup/lookup.go`:
    *   `defaultCacheSize = 100`
    *   `defaultCacheTTL = config.Duration(8 * time.Hour)`
    *   `defaultParallelLookups = 16`

*   Implement the `Lookup` struct in `plugins/processors/snmp_lookup/lookup.go`:
    *   Fields: AgentTag, IndexTag, Tags, embedded snmp.ClientConfig, CacheSize, ParallelLookups, Ordered, CacheTTL, MinTimeBetweenUpdates, Log, getConnectionFunc, cache.

*   Implement `Lookup` methods:
    *   `Init() error`: Validate SNMP client configuration. Return error 'parsing SNMP client config: invalid version' for invalid versions.
    *   `SampleConfig() string`: Return a TOML configuration string.
    *   `Start(acc telegraf.Accumulator) error` and `Stop()`: Initialize and tear down internal state.
    *   `Add(m telegraf.Metric, acc telegraf.Accumulator) error`: Handle metrics based on presence of agent and index tags.
    *   `updateAgent(agent string) *tagMap`: Perform SNMP walk and return a `*tagMap`.
    *   `getConnection(agent string) (snmp.Connection, error)`: Return error for unsupported agent URL schemes.

*   Define `tagMapRows` type and `tagMap` struct:
    *   `tagMapRows`: `map[string]map[string]string`
    *   `tagMap`: Fields `created time.Time` and `rows tagMapRows`.

*   Implement the `store` type in `plugins/processors/snmp_lookup/store.go`:
    *   Fields: `cache`, `inflight`, `deferredUpdates`, `update`, `notify`.
    *   Methods: `addBacklog(agent string, earliest time.Time)`, `lookup(agent string, index string)`, `destroy()`, `purge()`.

*   Implement `newStore` function:
    *   Signature: `newStore(size int, ttl config.Duration, workers int, minUpdateInterval config.Duration) *store`
    *   Constructs a store with specified parameters.

*   Ensure `Lookup.Ordered` outputs metrics in received order when true.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.