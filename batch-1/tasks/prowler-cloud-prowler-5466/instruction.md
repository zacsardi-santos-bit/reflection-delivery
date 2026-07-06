Implement a new service class in Prowler to audit AWS Step Functions state machines, ensuring they have logging enabled. Create a mechanism to list state machines, retrieve their configurations, and evaluate their logging status. Handle permission and resource errors gracefully.

*   Implement the `StepFunctions` class in `prowler/providers/aws/services/stepfunctions/stepfunctions_service.py`.
    *   Set the `service` attribute to 'stepfunctions'.
    *   Use boto3 SFN clients for `regional_clients` and a boto3 Session instance for `session`.
    *   Populate `state_machines` as a dictionary keyed by ARN, listing all state machines across regions.
    *   Populate each state machine with `name`, `arn`, `type`, `role_arn`, `status`, `logging_configuration`, `tracing_configuration`, `encryption_configuration`, and `tags`.
    *   Handle `AccessDeniedException` by keeping `state_machines` empty.
    *   Handle `ResourceNotFoundException` by setting `status` to 'ACTIVE' and configurations to None.
    *   Handle `InvalidParameterException` and `ResourceNotFoundException` by setting `tags` to an empty list.
    *   Ensure the service does not crash on non-ClientError exceptions, maintaining `state_machines` as a dictionary.

*   Implement the `stepfunctions_statemachine_logging_enabled` class in `prowler/providers/aws/services/stepfunctions/stepfunctions_statemachine_logging_enabled/stepfunctions_statemachine_logging_enabled.py`.
    *   Extend the Check base class.
    *   Implement `execute()` to return an empty list if no state machines are found.
    *   Return 'FAIL' with the message 'Step Functions state machine {name} does not have logging enabled.' if `logging_configuration` level is 'OFF'.
    *   Return 'PASS' with the message 'Step Functions state machine {name} has logging enabled.' if `logging_configuration` level is not 'OFF'.
    *   Include `resource_id`, `resource_arn`, and `region` in each check result.

*   Define the `StateMachine`, `LoggingConfiguration`, `LoggingLevel`, `TracingConfiguration`, and `EncryptionConfiguration` classes in `prowler/providers/aws/services/stepfunctions/stepfunctions_service.py` with specified attributes and types.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.