Implement a new security check in Prowler to verify AWS RDS event notifications for database security group events. Ensure the check evaluates whether both "configuration change" and "failure" event categories are monitored via active subscriptions. Extend the RDS service to retrieve event subscription data and produce findings based on the subscription coverage.

*   Extend the RDS service to expose event subscription data:
    *   Implement a list of `EventSubscription` objects as `db_event_subscriptions` in the RDS class.
    *   Populate `db_event_subscriptions` by calling the AWS `describe_event_subscriptions` API per region during service initialization.
    *   Include a placeholder entry with empty values and `enabled=False` for regions with no subscriptions.

*   Define the `EventSubscription` class in `prowler/providers/aws/services/rds/rds_service.py`:
    *   Include fields: `id`, `arn`, `sns_topic_arn`, `status`, `source_type`, `source_id`, `event_list`, `enabled`, `region`.

*   Implement the `rds_instance_event_subscription_security_groups` check class:
    *   Extend the `Check` class and implement the `execute()` method.
    *   Use the `rds_client` module-level variable for the shared RDS service client instance.
    *   Evaluate event subscriptions for each region and produce findings:
        *   Return no findings if the scan skips unused services and no RDS instances are present.
        *   Return a FAIL finding with specific messages if no enabled `db-security-group` subscription exists or if only one of the required categories is covered.
        *   Return a PASS finding if an enabled subscription covers all event categories.

*   Implement the `__get_trail_arn_template__` method in the RDS class:
    *   Return the account-level ARN string for a given region in the format `arn:{partition}:rds:{region}:{account}:account`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.