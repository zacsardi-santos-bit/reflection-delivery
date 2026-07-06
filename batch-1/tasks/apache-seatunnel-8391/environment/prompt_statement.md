I'm working with a Kafka source connector that reads change-data-capture events in debezium format. A single Kafka topic can contain events from multiple database tables, but I only want to process records from one specific table. Right now there's no way to configure the connector to filter by table — every record on the topic gets deserialized and passed downstream, even if it's from a table I don't care about. I'd like to add a configuration option for specifying a table filter (with database name, schema name, and table name) so only matching records are processed and everything else is silently skipped.

There are also a couple of related issues I need to address at the same time:

First, when the incoming debezium event does not contain a column that is defined in my schema, the deserialization currently fails or behaves incorrectly. It should instead populate that field as null.

Second, certain database connectors (like Oracle) sometimes do not include the database name in the source metadata of debezium records. The filtering logic should handle this gracefully — if an exact match on the full path fails, it should try matching using just the schema and table name.

Finally, there's also a bug in the Avro format serializer where rows containing small integer fields or maps with small integer values are not handled correctly. These should serialize and deserialize correctly in a round-trip.
