I'm working on the Kafka configuration command tool and I'd like to fix two related problems with how it handles configuration changes.

*   ConfigCommand.alterConfig must apply configuration changes by calling incrementalAlterConfigs directly, without first calling describeConfigs to read the existing configuration. This applies when altering topics, brokers, client metrics, and groups.

*   ConfigCommand.alterConfig must succeed without throwing any exception when --delete-config specifies configuration keys that do not currently exist on the target resource. The deletion of a non-existent key must be treated as a no-op.

*   ConfigCommand.alterConfig must succeed for delete operations on non-existent config keys across all supported entity types: topics, specific brokers (by entity name), and broker defaults (via --entity-default).


*   Interface details: Type: Method
Name: alterConfig
Location: tools/src/main/java/org/apache/kafka/tools/ConfigCommand.java
Signature: static void alterConfig(Admin adminClient, ConfigCommand.ConfigCommandOptions opts) throws Exception
Description: Alters configuration properties for a Kafka entity (topic, broker, client metrics, group). Must apply changes via incrementalAlterConfigs without first calling describeConfigs. Must not throw an exception when --delete-config specifies keys that do not currently exist on the resource. Supports entity types: topics, brokers (by name or --entity-default), client-metrics, and groups.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.