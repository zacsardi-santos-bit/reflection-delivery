Implement a mechanism to wait for newly created DynamoDB tables to become active before applying policies triggered by table-creation events. Ensure that this wait logic is only applied when resources are fetched via the live API and not when using the configuration history service. Normalize encryption metadata fields from the configuration history to match the live API format for consistent policy evaluation.

*   Implement waiting logic for DynamoDB table creation:
    *   In `c7n/resources/dynamodb.py`, create a `DescribeTable` class with a `get_waiter()` method.
        *   `get_waiter()` should return a tuple of a waiter object and a configuration dictionary.
        *   Ensure `get_resources(ids, *args, **kw)` calls `get_waiter()` and invokes the waiter for each table ID when triggered by a CreateTable event.
*   Avoid wait logic when using the configuration history:
    *   Ensure the waiter mechanism is not invoked when resources are fetched using the configuration history service.
*   Normalize encryption metadata fields:
    *   Create a `ConfigTable` class in `c7n/resources/dynamodb.py`, extending `query.ConfigSource`.
        *   Override `load_resource(item)` to normalize field names:
            *   Rename 'Ssedescription' to 'SSEDescription'.
            *   Rename 'KmsmasterKeyArn' to 'KMSMasterKeyArn'.
            *   Rename 'Ssetype' to 'SSEType'.
            *   Preserve the 'Status' field as is.
        *   Convert `CreationDateTime` and `BillingModeSummary.LastUpdateToPayPerRequestDateTime` from epoch-milliseconds to datetime objects using `datetime.fromtimestamp(value / 1000.0)`.
*   Ensure data consistency and proper return types:
    *   In `c7n/query.py`, modify `ConfigSource.get_resources` to return a list:
        *   Change the return statement from `return filter(None, results)` to `return list(filter(None, results))`.
    *   Ensure that a config-sourced DynamoDB table resource is equal to a describe-sourced resource after removing `ItemCount`, `CreationDateTime`, `TableSizeBytes`, and `BillingModeSummary`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.