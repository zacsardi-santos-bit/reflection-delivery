Implement a configuration option to isolate the internal metadata catalog table onto dedicated region servers in the HBase region balancer. Ensure this isolation works alongside existing replica distribution features and provide a method to verify table isolation status.

*   Update the `BalancerConditionals` class:
    *   Add a public static String constant `ISOLATE_META_TABLE_KEY` with the value `"hbase.master.balancer.stochastic.conditionals.isolateMetaTable"`.
    *   Implement `isConditionalBalancingEnabled()` to return true when any conditional balancing is active. Replace the existing `shouldSkipSloppyServerEvaluation()` method with this new method.
    *   Implement `isTableIsolationEnabled()` to return true when `MetaTableIsolationConditional` is active, which occurs when `ISOLATE_META_TABLE_KEY` is set to true in the configuration.
    *   Ensure that when `loadClusterState` is called and `ISOLATE_META_TABLE_KEY` is true, the `MetaTableIsolationConditional` class is activated, making `isTableIsolationEnabled()` return true.

*   Create the `MetaTableIsolationConditional` class in the `org.apache.hadoop.hbase.master.balancer` package:
    *   Extend `TableIsolationConditional`.
    *   Activate this class by setting `ISOLATE_META_TABLE_KEY` to true.
    *   Implement `isRegionToIsolate(RegionInfo regionInfo)` to return true for meta regions using `regionInfo.isMetaRegion()`.

*   Ensure the balancer:
    *   When meta table isolation is enabled, no server should host both meta table regions and regions from any other table after balancing.
    *   Supports simultaneous use of replica distribution, ensuring meta regions are isolated and all region replicas are distributed across separate servers when both features are enabled.

*   Update the `CandidateGeneratorTestUtil` class:
    *   Implement a static method `isTableIsolated(BalancerClusterState cluster, TableName tableName, String tableType)` that returns false if any server hosts both regions of the specified table and other tables, and true if no such co-location exists.
    *   Log debug messages identifying violating servers when isolation is broken and confirm success when isolation holds.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.