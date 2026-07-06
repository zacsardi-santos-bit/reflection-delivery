## Description

The automatic resource provisioning feature for Cloudflare Workers deployments needs several improvements to make the output cleaner and the flow more reliable.

**Output verbosity**: When a resource is successfully provisioned, the current output appends the resource's name to the success message and follows it with a separator line. This is noisier than necessary — the success message should be simplified.

**Unnecessary confirmation dialogs**: When a database or bucket name is already specified in the worker configuration file, the provisioning flow still prompts the user for confirmation before creating the resource. If the name is already in the config, the intent is clear — no confirmation should be required.

**D1 database name lookup**: Unlike R2 buckets, D1 databases are not currently checked by name before provisioning begins. If a database with the specified name already exists on the account, the deployment should detect this and reuse the existing database (by its identifier) rather than treating it as a resource that needs provisioning.

**R2 jurisdiction inheritance bug**: When inheriting an R2 bucket binding from an existing worker deployment, the system does not account for jurisdiction mismatches. If the existing binding uses a bucket in one geographic jurisdiction but the current configuration specifies a different jurisdiction, the bucket should not be reused — a new bucket should be provisioned in the correct jurisdiction instead.

## Expected Behavior
- Provisioning success messages are simplified (no resource name appended, no separator lines)
- No confirmation dialogs when resource names are pre-specified in configuration
- D1 databases are looked up by name before provisioning; existing ones are reused automatically
- R2 bucket jurisdiction mismatches prevent inheritance and trigger re-provisioning

## Why This Matters
These improvements make the provisioning flow consistent, non-interactive when configuration is explicit, and correct when resource names or jurisdictions are specified in the config file.
