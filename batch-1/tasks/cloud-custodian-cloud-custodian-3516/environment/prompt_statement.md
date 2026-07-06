I'm working on a cloud custodian policy that triggers when a DynamoDB table is created, and I'm running into issues with how table resources are fetched in different contexts.

When the policy triggers on a table creation event, it fails because the table isn't fully active yet by the time custodian tries to work with it — tables can't be tagged or acted upon while they're still being created. I need the system to wait for the table to become available before proceeding, with a configurable timeout. This wait logic should be isolated to the live API fetch path and should not apply when table data comes from a configuration history service.

On the configuration history side, I also need the encryption metadata to be normalized properly. The configuration history stores encryption field names in a different casing than what the live API returns, which causes policies that inspect encryption settings to behave differently depending on which source is used. Ideally, both sources should produce resource data in a consistent format so policy filters work the same regardless of how the data was obtained.

There's also a related issue where results from the configuration source aren't returned as a proper list, which breaks downstream code that tries to measure result counts or compare resources.
