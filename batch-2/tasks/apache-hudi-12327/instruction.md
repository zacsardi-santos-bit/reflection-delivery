Implement the necessary logic in the `EightToSevenDowngradeHandler` and `SevenToEightUpgradeHandler` classes to ensure proper migration of table configuration properties during version transitions in Apache Hudi. Add static utility methods to handle the upgrade and downgrade of specific properties.

Requirements:

* Implement `EightToSevenDowngradeHandler` methods:
    * `downgradePartitionFields(HoodieWriteConfig config, HoodieTableConfig tableConfig, Map<ConfigProperty, String> tablePropsToAdd)`
        * Read the partition path field from the write config using `KeyGeneratorOptions.PARTITIONPATH_FIELD_NAME`.
        * Add an entry for `PARTITION_FIELDS` into `tablePropsToAdd` if a custom key generator class is in use and a partition path field is configured.
    * `unsetInitialVersion(HoodieTableConfig tableConfig, Map<ConfigProperty, String> tablePropsToAdd)`
        * Remove `INITIAL_VERSION` from the table config's `TypedProperties`.
    * `unsetRecordMergeMode(HoodieTableConfig tableConfig, Map<ConfigProperty, String> tablePropsToAdd)`
        * Remove `RECORD_MERGE_MODE` from the table config's `TypedProperties`.
        * Add a `PAYLOAD_CLASS_NAME` entry into `tablePropsToAdd` derived from the existing merge mode configuration.
    * `downgradeBootstrapIndexType(HoodieTableConfig tableConfig, Map<ConfigProperty, String> tablePropsToAdd)`
        * Do not add `BOOTSTRAP_INDEX_TYPE` or `BOOTSTRAP_INDEX_CLASS_NAME` if no applicable class name is set.
        * Remove `BOOTSTRAP_INDEX_TYPE` from the table config's properties.
    * `downgradeKeyGeneratorType(HoodieTableConfig tableConfig, Map<ConfigProperty, String> tablePropsToAdd)`
        * Do not add `KEY_GENERATOR_TYPE` or `KEY_GENERATOR_CLASS_NAME` if no applicable class name is set.
        * Remove `KEY_GENERATOR_TYPE` from the table config's properties.

* Implement `SevenToEightUpgradeHandler` methods:
    * `upgradePartitionFields(HoodieWriteConfig config, HoodieTableConfig tableConfig, Map<ConfigProperty, String> tablePropsToAdd)`
        * Read the partition path field from the write config.
        * Add a `PARTITION_FIELDS` entry into `tablePropsToAdd` if a custom key generator class is in use.
    * `setInitialVersion(HoodieTableConfig tableConfig, Map<ConfigProperty, String> tablePropsToAdd)`
        * Add an entry for `INITIAL_VERSION` into `tablePropsToAdd` with the value "6".
    * `upgradeMergeMode(HoodieTableConfig tableConfig, Map<ConfigProperty, String> tablePropsToAdd)`
        * Derive the record merge mode from the table config's payload class.
        * Add a non-null `RECORD_MERGE_MODE` entry into `tablePropsToAdd`.
    * `upgradeBootstrapIndexType(HoodieTableConfig tableConfig, Map<ConfigProperty, String> tablePropsToAdd)`
        * Add `BOOTSTRAP_INDEX_CLASS_NAME` and `BOOTSTRAP_INDEX_TYPE` entries into `tablePropsToAdd` if bootstrap index is enabled and the class name is set.
    * `upgradeKeyGeneratorType(HoodieTableConfig tableConfig, Map<ConfigProperty, String> tablePropsToAdd)`
        * Add `KEY_GENERATOR_CLASS_NAME` and `KEY_GENERATOR_TYPE` entries into `tablePropsToAdd` based on the configured key generator class.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.