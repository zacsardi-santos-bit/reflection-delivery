I'm poking at the DataHub Airflow integration plugin, the bit that captures lineage by tracking which datasets each task reads and writes. Right now when the plugin hits a dataset as an inlet or outlet it emits this low-value "this entity exists and isn't deleted" status signal, which is basically a no-op for anyone using the catalog. It doesn't tell you anything.

What I want instead is for it to emit the dataset's actual key identity metadata, the structural info that's already fully baked into the dataset's identifier so there's no extra network lookup or config needed. That means for each dataset I want the data platform, the name, and the environment/origin (the thing sometimes called origin) coming through as proper key aspect metadata rather than the existence flag.

This needs to apply everywhere the plugin processes datasets as task inputs or outputs, so both the regular lineage path and the path where task execution capture is turned on, they should behave the same way. And it should hold for all the supported platforms, the various cloud data warehouses as well as local databases, not just one of them.

So basically, drop the superfluous existence signal in @metadata-ingestion-modules/airflow-plugin and replace it with the platform, name, and env identity aspect for every inlet and outlet dataset. Same output whether or not execution capture is enabled.
