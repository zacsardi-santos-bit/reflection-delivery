I'm working on Apache Pinot and need to add a pluggable validation hook so that external code can register custom validators that are automatically invoked before instance or table configurations are committed to the cluster.

*   InstanceUtils must provide a static method toInstance(InstanceConfig instanceConfig) that reconstructs a pinot-spi Instance from a Helix InstanceConfig, performing the reverse of toHelixInstanceConfig. The method must correctly restore host, port, type, tags, pools, optional ports (grpc, admin, query service, query mailbox), and the queriesDisabled flag.

*   InstanceUtils.toInstance must determine the InstanceType from the instance ID prefix (Server_, Broker_, Controller_, Minion_) rather than from any other field.

*   InstanceUtils.toInstance must handle legacy SERVER instance hostnames that include the 'Server_' prefix by stripping that prefix to return the bare hostname.

*   InstanceUtils.toInstance must return -1 for any optional port (grpc, admin, query service, query mailbox) that is not present in the InstanceConfig, rather than returning 0.

*   InstanceUtils.toInstance must throw IllegalArgumentException when the instance ID does not match any known instance type prefix. The exception message must contain the unknown instance ID string.

*   InstanceConfigValidatorRegistry must be a class at org.apache.pinot.spi.config.instance.InstanceConfigValidatorRegistry providing static methods: register (accepts a functional validator that takes an Instance and may throw ConfigValidationException), validate (takes an Instance, invokes all registered validators in registration order), and reset (clears all registered validators).

*   InstanceConfigValidatorRegistry.validate must be a no-op when no validators have been registered. When a validator throws ConfigValidationException, validate must propagate that exception and skip all subsequent validators (short-circuit on first rejection).

*   TableConfigValidatorRegistry must be a class at org.apache.pinot.spi.config.table.TableConfigValidatorRegistry providing static methods: register (accepts a functional validator taking a TableConfig and Schema), validate (takes a TableConfig and Schema, runs all validators in order), and reset (clears all validators). It must exhibit the same no-op, short-circuit, and ordering behavior as InstanceConfigValidatorRegistry.

*   ConfigValidationException must be a class at org.apache.pinot.spi.exception.ConfigValidationException that accepts a String message and is throwable by validators.

*   PinotHelixResourceManager.addInstance must invoke InstanceConfigValidatorRegistry.validate on the instance before persisting it to Helix. If validation throws ConfigValidationException, the instance must NOT be written to Helix and the exception must propagate to the caller.

*   PinotHelixResourceManager.updateInstance must invoke InstanceConfigValidatorRegistry.validate on the new instance before persisting the updated config. If validation throws ConfigValidationException, the config must NOT be persisted and the exception must propagate.

*   PinotHelixResourceManager.updateInstanceTags must apply the new tags to an in-memory copy of the instance, then invoke InstanceConfigValidatorRegistry.validate on the resulting instance before persisting. The validator must receive an Instance with the new tags already applied. If validation throws ConfigValidationException, the config must NOT be persisted and the exception must propagate.


*   Interface details: Type: Class
Name: InstanceConfigValidatorRegistry
Location: pinot-spi/src/main/java/org/apache/pinot/spi/config/instance/InstanceConfigValidatorRegistry.java
Description: Static registry for instance configuration validators. Validators are functional interfaces that accept an Instance and throw ConfigValidationException on failure.
Signature: static void register(InstanceConfigValidator validator)
Signature: static void validate(Instance instance) throws ConfigValidationException
Signature: static void reset()

Type: Class
Name: TableConfigValidatorRegistry
Location: pinot-spi/src/main/java/org/apache/pinot/spi/config/table/TableConfigValidatorRegistry.java
Description: Static registry for table configuration validators. Validators are functional interfaces that accept a TableConfig and Schema and throw ConfigValidationException on failure.
Signature: static void register(TableConfigValidator validator)
Signature: static void validate(TableConfig tableConfig, Schema schema) throws ConfigValidationException
Signature: static void reset()

Type: Class
Name: ConfigValidationException
Location: pinot-spi/src/main/java/org/apache/pinot/spi/exception/ConfigValidationException.java
Description: Exception thrown by a validator when an instance or table configuration fails validation. Must carry a String message accessible via getMessage().
Signature: ConfigValidationException(String message)

Type: Function
Name: toInstance
Location: pinot-common/src/main/java/org/apache/pinot/common/utils/config/InstanceUtils.java
Signature: public static Instance toInstance(InstanceConfig instanceConfig)
Description: Converts a Helix InstanceConfig into a pinot-spi Instance. Determines InstanceType from the instance ID prefix (Server_, Broker_, Controller_, Minion_). Strips the "Server_" prefix from legacy server hostnames. Returns -1 for any optional port (grpc, admin, query service, query mailbox) absent from the config. Throws IllegalArgumentException with the instance ID in the message if the prefix does not match any known type.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.