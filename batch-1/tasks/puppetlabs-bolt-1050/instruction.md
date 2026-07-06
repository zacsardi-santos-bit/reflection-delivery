Update the inventory plugin system in Bolt to unify the syntax for target-discovery and configuration plugins. Ensure that both types of plugins use an inline underscore-prefixed syntax within their respective sections. Update hook names for consistency and provide clear error messages for unsupported capabilities or outdated syntax.

*   Update the Bolt::Plugin::Prompt class:
    *   Implement hooks() to return `['inventory_config']`.
    *   Implement validate_inventory_config(opts) to raise Bolt::ValidationError if 'message' is absent.
    *   Implement inventory_config(opts) to prompt the user, read input, and return the value.

*   Update the Bolt::Plugin::Puppetdb class:
    *   Implement hooks() to return `['inventory_targets']`.
    *   Implement inventory_targets(opts) to query PuppetDB and return target hashes.

*   Update the Bolt::Plugin::Terraform class:
    *   Implement hooks() to return `['inventory_targets']`.
    *   Implement inventory_targets(opts) to read a Terraform state file and return target hashes.

*   Update the Bolt::Inventory::Group2 class:
    *   Raise a ValidationError if 'target-lookups' key is present in data with the message matching /'target-lookups' are no longer/.
    *   Raise a ValidationError if a config entry references a non-existent plugin with the message matching /unknown plugin: "<name>"/.
    *   Raise a ValidationError if a target entry references a plugin without 'inventory_targets' in hooks with the message matching /<plugin_name> does not support inventory_targets/.
    *   Raise a ValidationError if a config entry references a plugin without 'inventory_config' in hooks with the message matching /<plugin_name> does not support inventory_config/.

*   Ensure plugin-based target discovery is specified using the 'targets' array with a '_plugin' key.
*   Ensure plugins used for target discovery advertise 'inventory_targets' in their hooks array.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.