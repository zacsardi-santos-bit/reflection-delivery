I'm working on the automatic resource provisioning feature in the Cloudflare Workers deployment tool and need a few things fixed.

First, when a resource finishes provisioning, the output currently shows the resource's name in the success message and then prints a separator line. I'd like both of those removed — just a clean success indicator is enough.

Second, when a database or bucket name is already specified in the configuration file, the tool still asks the user for confirmation before creating it. That confirmation dialog should be eliminated. If the name is already in the config, the tool should just go ahead and create the resource.

Third, the D1 database provisioning flow doesn't currently check whether a database with the given name already exists. The R2 bucket flow already handles this correctly — if a bucket with the specified name exists, it gets reused. D1 needs the same treatment: look up the database by name, and if one is found, use it directly instead of going through provisioning. If the lookup returns a "not found" error, proceed to create the database.

Finally, there's a bug with R2 bucket inheritance. When deciding whether to inherit an existing R2 bucket binding from a previous deployment, the tool isn't considering the geographic jurisdiction. If the existing binding's jurisdiction doesn't match what's specified in the current configuration, the bucket should not be inherited — a new one should be provisioned in the correct jurisdiction.
