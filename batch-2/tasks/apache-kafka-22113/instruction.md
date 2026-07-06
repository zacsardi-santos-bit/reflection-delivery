I'm working with Kafka's configuration management tool and I've noticed two issues I'd like to fix.

*   The ConfigCommand.alterConfig method must NOT call describeConfigs before applying configuration changes. It must directly call incrementalAlterConfigs without first fetching the current state of the resource's configuration.

*   The ConfigCommand.alterConfig method must call incrementalAlterConfigs and await its completion result for all entity types: topics, brokers (by name), brokers (by default/entity-default), client metrics, and groups.

*   When --delete-config specifies configuration keys that do not exist on the target resource, ConfigCommand.alterConfig must complete successfully without throwing an exception. The operation must be treated as a no-op success (idempotent delete).

*   The idempotent delete behavior must apply to all supported entity types: topics (--entity-type topics), named brokers (--entity-type brokers --entity-name), and default brokers (--entity-type brokers --entity-default).


*   Interface details: Type: Method
Name: alterConfig
Location: tools/src/main/java/org/apache/kafka/tools/ConfigCommand.java (entry point called by tests); implementation may delegate to core/src/main/scala/kafka/admin/ConfigCommand.scala
Signature: static void alterConfig(Admin client, ConfigCommand.ConfigCommandOptions opts) throws Exception
Description: Alters configuration for the specified entity (topic, broker, client metrics, group). Must call incrementalAlterConfigs directly without first calling describeConfigs. Must succeed (not throw an exception) when --delete-config specifies keys that do not exist on the target resource. The actual logic that needs to change resides in the private alterResourceConfig helper in core/src/main/scala/kafka/admin/ConfigCommand.scala, which must be updated to: (1) remove the pre-read of existing configs via describeConfigs, and (2) remove the check that throws an exception for non-existent config keys being deleted.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.