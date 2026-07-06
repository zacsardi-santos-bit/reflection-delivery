I'm working with the Bedrock AgentCore Runtime delete operator in Apache Airflow's Amazon provider.

*   BedrockDeleteAgentRuntimeOperator must accept wait_for_completion (bool, default True), waiter_delay (int, default 60), waiter_max_attempts (int, default 20), and deferrable (bool, default False) parameters in its constructor.

*   When BedrockDeleteAgentRuntimeOperator is executed with wait_for_completion=True and deferrable=False, it must call the 'agent_runtime_deleted' waiter with agentRuntimeId and WaiterConfig={'Delay': 60, 'MaxAttempts': 20} (using the configured waiter_delay and waiter_max_attempts values).

*   When BedrockDeleteAgentRuntimeOperator is executed with deferrable=True, it must defer using a BedrockAgentRuntimeDeletedTrigger and method_name='execute_complete'. The trigger must serialize with agent_runtime_id, waiter_delay=60, and waiter_max_attempts=20. Deferrable mode takes precedence over wait_for_completion.

*   When BedrockDeleteAgentRuntimeOperator is executed with wait_for_completion=False and deferrable=False, it must not invoke any waiter.

*   BedrockDeleteAgentRuntimeOperator must have an execute_complete(context, event) method that returns None when the event status is 'success', and raises RuntimeError when the event status is not 'success'.

*   BedrockAgentRuntimeDeletedTrigger must serialize its classpath as 'airflow.providers.amazon.aws.triggers.bedrock.BedrockAgentRuntimeDeletedTrigger' and include agent_runtime_id in the serialized kwargs.

*   BedrockAgentRuntimeDeletedTrigger.run() must use the 'agent_runtime_deleted' waiter name via BedrockAgentCoreControlHook, and upon success yield a TriggerEvent with {'status': 'success', 'agent_runtime_id': <agent_runtime_id>}.

*   The BedrockAgentCoreControlHook custom waiters list must include an 'agent_runtime_deleted' waiter entry.

*   The 'agent_runtime_deleted' waiter must use the GetAgentRuntime operation, reach success state when a ResourceNotFoundException is raised, retry when the runtime status is 'DELETING', and enter failure state when the status is 'CREATE_FAILED', 'UPDATE_FAILED', or 'READY'.


*   Interface details: Type: Class
Name: BedrockDeleteAgentRuntimeOperator
Location: providers/amazon/src/airflow/providers/amazon/aws/operators/bedrock.py
Description: Operator that deletes a Bedrock AgentCore Runtime. Must be updated to accept wait_for_completion, waiter_delay, waiter_max_attempts, and deferrable parameters, and to include an execute_complete method for async deferred completion handling.
Signature: __init__(self, *, agent_runtime_id: str, wait_for_completion: bool = True, waiter_delay: int = 60, waiter_max_attempts: int = 20, deferrable: bool = False, **kwargs)
Signature: execute(self, context: Context) -> None
Signature: execute_complete(self, context: Context, event: dict | None = None) -> None

Type: Class
Name: BedrockAgentRuntimeDeletedTrigger
Location: providers/amazon/src/airflow/providers/amazon/aws/triggers/bedrock.py
Description: Async trigger that waits for a Bedrock AgentCore Runtime to be fully deleted, using the "agent_runtime_deleted" custom waiter on BedrockAgentCoreControlHook. Serializes its classpath as "airflow.providers.amazon.aws.triggers.bedrock.BedrockAgentRuntimeDeletedTrigger". When the waiter succeeds, yields a TriggerEvent with {"status": "success", "agent_runtime_id": <agent_runtime_id>}.
Signature: __init__(self, *, agent_runtime_id: str, waiter_delay: int = 60, waiter_max_attempts: int = 20, aws_conn_id: str | None = None) -> None
Signature: serialize(self) -> tuple[str, dict]  — returns (classpath, {"agent_runtime_id": ..., "waiter_delay": ..., "waiter_max_attempts": ...})
Signature: hook(self) -> AwsGenericHook  — returns BedrockAgentCoreControlHook instance

Type: File (JSON waiter definition)
Name: agent_runtime_deleted waiter
Location: providers/amazon/src/airflow/providers/amazon/aws/waiters/bedrock-agentcore-control.json
Description: Custom waiter definition named "agent_runtime_deleted" that must be added to the existing waiters JSON file. Uses the GetAgentRuntime operation. Success state: when ResourceNotFoundException is raised (resource no longer exists). Retry state: when status equals "DELETING". Failure states: when status is "CREATE_FAILED", "UPDATE_FAILED", or "READY". Default delay: 60 seconds, maxAttempts: 20.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.