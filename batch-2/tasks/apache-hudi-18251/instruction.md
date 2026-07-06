I'm working on a large Apache Hudi table and need a way to run clustering incrementally based on the table's commit history.

*   CommitBasedClusteringPlanStrategy must be a public generic class (four type parameters) in the package org.apache.hudi.table.action.cluster.strategy, located at hudi-client/hudi-client-common/src/main/java/org/apache/hudi/table/action/cluster/strategy/CommitBasedClusteringPlanStrategy.java. It must extend PartitionAwareClusteringPlanStrategy and have a constructor accepting (HoodieTable, HoodieEngineContext, HoodieWriteConfig).

*   CommitBasedClusteringPlanStrategy must define a public static final String constant named CLUSTERING_COMMIT_CHECKPOINT_KEY that can be used as a key in the extra metadata map.

*   If the earliest commit to cluster configuration property is not set (returns null), generateClusteringPlan() must immediately return an empty Option without processing any commits.

*   The generateClusteringPlan() method must return an empty Option (not present) when the table's commits timeline contains no completed commits after the earliest commit time, and must return an empty Option when no eligible files are found for clustering.

*   When eligible files exist across one or more commits, generateClusteringPlan() must return a non-empty Option<HoodieClusteringPlan> whose getInputGroups() list contains HoodieClusteringGroup entries, each with getSlices() returning HoodieSliceInfo entries whose getDataFilePath() and getFileId() reflect the clustered files.

*   Files from different partitions must always be placed into separate HoodieClusteringGroup entries; files within the same partition are grouped together using the inherited buildClusteringGroupsForPartition() method, which splits them into multiple groups when total size exceeds the configured maximum bytes per group.

*   Files whose file groups have been superseded by replace commits (REPLACE_COMMIT_ACTION) must be excluded from the clustering plan; only non-replaced file groups should appear in the plan's input groups.

*   Delta commits (DELTA_COMMIT_ACTION) must be handled: log-file write stats are included as file slices. A log-only file group (one with no base file) must be passed to buildClusteringGroupsForPartition() from the parent class, which assigns parquetMaxFileSize to such log-only slices; this causes them to form their own group rather than being merged with smaller file groups when the max bytes per group threshold is smaller than parquetMaxFileSize.

*   The getExtraMetadata() method must return a Map<String, String> containing the key CLUSTERING_COMMIT_CHECKPOINT_KEY. When commits were processed, its value must equal the requestedTime of the last processed commit instant. When no commits were processed (empty timeline or config not set), the value for that key must be null.

*   HoodieClusteringConfig.Builder must expose a method withClusteringPlanEarliestCommitToCluster(String earliestCommit) that stores the provided commit time in the PLAN_STRATEGY_EARLIEST_COMMIT_TO_CLUSTER config property and returns the Builder for chaining.

*   HoodieClusteringConfig must declare a public static ConfigProperty<String> field named PLAN_STRATEGY_EARLIEST_COMMIT_TO_CLUSTER whose config key is the CLUSTERING_STRATEGY_PARAM_PREFIX concatenated with 'earliest.commit.to.cluster', with no default value. When this property is set, the strategy must only consider commits with a requested time strictly after this value.


*   Interface details: Type: Class
Name: CommitBasedClusteringPlanStrategy
Location: hudi-client/hudi-client-common/src/main/java/org/apache/hudi/table/action/cluster/strategy/CommitBasedClusteringPlanStrategy.java
Description: A commit-aware clustering plan strategy that iterates through the table's commit history and builds a clustering plan by grouping files from each commit by partition and respecting per-group size limits. Tracks progress via a checkpoint key in extra metadata. Must be a public generic class with four type parameters (T, I, K, O) that extends PartitionAwareClusteringPlanStrategy<T, I, K, O> (also in package org.apache.hudi.table.action.cluster.strategy). The class must delegate to the inherited buildClusteringGroupsForPartition() method from the parent class, which handles log-only file slices by assigning them parquetMaxFileSize for grouping purposes (causing log-only slices to form their own group when MAX_BYTES_PER_GROUP is smaller than parquetMaxFileSize).
Signature: CommitBasedClusteringPlanStrategy(HoodieTable<T,I,K,O> table, HoodieEngineContext engineContext, HoodieWriteConfig writeConfig)

Constant: CLUSTERING_COMMIT_CHECKPOINT_KEY
Type: public static final String
Owner: CommitBasedClusteringPlanStrategy
Description: Key used in the extra metadata map to store the requestedTime of the last commit processed when building the clustering plan. Must be importable via static import from CommitBasedClusteringPlanStrategy.

Method: generateClusteringPlan
Owner: CommitBasedClusteringPlanStrategy
Signature: generateClusteringPlan() -> Option<HoodieClusteringPlan>
Description: Generates a clustering plan by scanning completed commits from the table timeline. Returns an empty Option immediately when getClusteringEarliestCommitToCluster() returns null (config not set). Returns an empty Option when there are no commits after the earliest commit time, or when no eligible files are found. Returns a non-empty Option<HoodieClusteringPlan> with one or more HoodieClusteringGroup entries when eligible files exist. Groups files by partition (files from different partitions are always in separate groups) and by size using buildClusteringGroupsForPartition (files within a partition are split into multiple groups when their total size exceeds the configured max bytes per group). Files whose file groups have been superseded by replace commits are excluded from the plan.

Method: getExtraMetadata
Owner: CommitBasedClusteringPlanStrategy
Signature: getExtraMetadata() -> Map<String, String>
Description: Returns a map of extra metadata to persist with the clustering plan. The map must contain the key CLUSTERING_COMMIT_CHECKPOINT_KEY mapped to the requestedTime of the last commit instant processed during plan generation. If no commits were processed (empty timeline or no earliest commit configured), the map entry for CLUSTERING_COMMIT_CHECKPOINT_KEY must be null.

Type: Method addition to existing class HoodieClusteringConfig.Builder
Name: withClusteringPlanEarliestCommitToCluster
Location: hudi-client/hudi-client-common/src/main/java/org/apache/hudi/config/HoodieClusteringConfig.java
Signature: withClusteringPlanEarliestCommitToCluster(String earliestCommit) -> HoodieClusteringConfig.Builder
Description: Configures the earliest commit time (exclusive) from which to start considering commits for the commit-based clustering plan strategy. Sets the PLAN_STRATEGY_EARLIEST_COMMIT_TO_CLUSTER config property.

Type: Field addition to existing class HoodieClusteringConfig
Name: PLAN_STRATEGY_EARLIEST_COMMIT_TO_CLUSTER
Location: hudi-client/hudi-client-common/src/main/java/org/apache/hudi/config/HoodieClusteringConfig.java
Description: ConfigProperty<String> for the earliest commit to cluster. Config key must be constructed from the CLUSTERING_STRATEGY_PARAM_PREFIX concatenated with "earliest.commit.to.cluster". Must have no default value.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.