I'm working on the MLflow AI gateway and need to add a guardrail system that enforces content policies on requests and responses flowing through the gateway.

*   The run_before_guardrails function must be async, accept a list of guardrail objects and a request payload dict, execute only BEFORE-stage guardrails (skipping AFTER-stage ones without invoking their scorer), and return the original request payload if all pass.

*   run_before_guardrails must raise GuardrailViolation with a message containing the word 'blocked' at the first failing guardrail, and must not invoke any subsequent guardrails after the first failure.

*   The run_after_guardrails function must be async, accept a list of guardrail objects, a request payload, and a ResponsePayload, execute only AFTER-stage guardrails (skipping BEFORE-stage ones without invoking their scorer), and return the original response if all pass or the list is empty.

*   run_after_guardrails must raise GuardrailViolation with a message containing the word 'blocked' at the first failing AFTER-stage guardrail.

*   The load_guardrails function must call store.list_endpoint_guardrail_configs(endpoint_config.endpoint_id), and return an empty list when that call returns no configs.

*   For each guardrail config returned by load_guardrails, the function must call store.resolve_endpoint_in_scorer(config.guardrail.scorer), replace the guardrail entity's scorer with the resolved scorer, then call JudgeGuardrail.from_entity(mutated_guardrail, server_url) where server_url is the request base_url with any trailing slash stripped (e.g., 'http://localhost:5000/' becomes 'http://localhost:5000').

*   load_guardrails must silently skip any guardrail config for which JudgeGuardrail.from_entity raises an exception, and include only successfully converted guardrails in the returned list.

*   GuardrailViolation must be an exception class importable from mlflow.gateway.guardrails.

*   JudgeGuardrail must be a class importable from mlflow.gateway.guardrails with constructor parameters scorer, stage (GuardrailStage), action (GuardrailAction), and name (str), and a classmethod from_entity(guardrail_entity, server_url) -> JudgeGuardrail.

*   _SANITIZE_BYPASS_HEADER must be a string constant importable from mlflow.gateway.guardrails; when a request's headers contain this key with the exact string value '1', the gateway invocations handler must skip guardrail loading entirely (load_guardrails is not called); any other header value (e.g. 'true') must not bypass guardrails.

*   GatewayGuardrail, GatewayGuardrailConfig, GuardrailAction, and GuardrailStage must be importable from mlflow.entities.gateway_guardrail; GuardrailAction must support values 'VALIDATION' and 'SANITIZATION'; GuardrailStage must support values 'BEFORE' and 'AFTER'.

*   The invocations and chat_completions gateway API handlers must call load_guardrails to retrieve guardrails, run BEFORE-stage guardrails before calling the provider, and run AFTER-stage guardrails after receiving the provider's response; a VALIDATION failure at either stage must return an HTTP 400 error without calling the provider (for BEFORE failures).

*   When a guardrail has action SANITIZATION and the scorer fails, the gateway must call the configured action endpoint via send_request to rewrite the content; for BEFORE-stage sanitization the rewritten content replaces the request payload forwarded to the provider; for AFTER-stage sanitization the rewritten content replaces the response returned to the caller.

*   When a SANITIZATION guardrail has no configured action endpoint and the scorer fails, the gateway must return an HTTP 400 error.

*   When multiple guardrails are configured on the same endpoint, they must be executed in ascending execution_order, regardless of the order they were added to the database.


*   Interface details: Type: Module
Name: mlflow.gateway.guardrail_utils
Location: mlflow/gateway/guardrail_utils.py
Description: New module providing three functions for loading and running guardrails in the MLflow gateway.

Type: Function
Name: run_before_guardrails
Location: mlflow/gateway/guardrail_utils.py
Signature: run_before_guardrails(guardrails: list, request_payload: dict) -> dict
Description: Async function. Filters the guardrail list to only BEFORE-stage entries and runs each in order. Returns the request payload unchanged if all pass. Raises GuardrailViolation (message matches "blocked") at the first failure and stops processing any remaining guardrails.

Type: Function
Name: run_after_guardrails
Location: mlflow/gateway/guardrail_utils.py
Signature: run_after_guardrails(guardrails: list, request_payload: dict, response_payload: ResponsePayload) -> ResponsePayload
Description: Async function. Filters the guardrail list to only AFTER-stage entries and runs each in order. Returns the response payload unchanged if all pass or if the list is empty. Raises GuardrailViolation (message matches "blocked") at the first failure.

Type: Function
Name: load_guardrails
Location: mlflow/gateway/guardrail_utils.py
Signature: load_guardrails(store, endpoint_config, request) -> list
Description: Synchronous function. Calls store.list_endpoint_guardrail_configs(endpoint_config.endpoint_id) to retrieve configs. For each config, calls store.resolve_endpoint_in_scorer(config.guardrail.scorer) to get a resolved scorer, replaces the guardrail's scorer with the resolved one, then calls JudgeGuardrail.from_entity(mutated_guardrail, server_url) where server_url is request.base_url with any trailing slash stripped. If from_entity raises any exception for a given config, that entry is silently skipped. Returns a list of JudgeGuardrail instances.

Type: Class
Name: GuardrailViolation
Location: mlflow/gateway/guardrails.py
Description: Exception raised when a guardrail blocks a request or response. The error message must contain the word "blocked".

Type: Class
Name: JudgeGuardrail
Location: mlflow/gateway/guardrails.py
Description: Guardrail implementation backed by a scorer. Constructor accepts scorer, stage (GuardrailStage), action (GuardrailAction), and name. Exposes a class method from_entity(guardrail_entity, server_url) that constructs a JudgeGuardrail from a GatewayGuardrail entity and a server URL string. from_entity must use Scorer.model_validate (from mlflow.genai.scorers.base) to deserialize the scorer from the guardrail entity's serialized_scorer data.
Signature: __init__(self, scorer, stage: GuardrailStage, action: GuardrailAction, name: str)
Signature: from_entity(cls, guardrail_entity: GatewayGuardrail, server_url: str) -> JudgeGuardrail

Type: Constant
Name: _SANITIZE_BYPASS_HEADER
Location: mlflow/gateway/guardrails.py
Description: HTTP header name constant. When an incoming request contains this header with the exact value "1", the gateway skips all guardrail evaluation for that request. Any other value (e.g. "true") does NOT bypass guardrails.

Type: Class
Name: GatewayGuardrail
Location: mlflow/entities/gateway_guardrail.py
Description: Entity representing a guardrail definition. Fields: guardrail_id (str), name (str), scorer (ScorerVersion), stage (GuardrailStage), action (GuardrailAction), created_at (int), last_updated_at (int).

Type: Class
Name: GatewayGuardrailConfig
Location: mlflow/entities/gateway_guardrail.py
Description: Entity representing the association of a guardrail with an endpoint. Fields: endpoint_id (str), guardrail_id (str), execution_order (int), created_at (int), guardrail (GatewayGuardrail).

Type: Enum
Name: GuardrailAction
Location: mlflow/entities/gateway_guardrail.py
Description: Enum with values "VALIDATION" and "SANITIZATION". VALIDATION blocks on failure; SANITIZATION rewrites content via an action endpoint on failure.

Type: Enum
Name: GuardrailStage
Location: mlflow/entities/gateway_guardrail.py
Description: Enum with values "BEFORE" (run before the provider call) and "AFTER" (run after the provider call).

Type: Function
Name: invocations (integration behavior)
Location: mlflow/server/gateway_api.py
Description: The existing invocations endpoint must call load_guardrails to retrieve active guardrails for the endpoint, run BEFORE-stage guardrails on the incoming request before calling the provider, and run AFTER-stage guardrails on the response before returning it. If _SANITIZE_BYPASS_HEADER is present in the request with value exactly "1", load_guardrails must NOT be called. VALIDATION failures at any stage raise HTTP 400. SANITIZATION failures rewrite the content using the action endpoint via send_request (from mlflow.gateway.guardrails). A SANITIZATION guardrail with no action endpoint raises HTTP 400 on failure. Guardrails are applied in ascending execution_order.

Type: Function
Name: chat_completions (integration behavior)
Location: mlflow/server/gateway_api.py
Description: The existing chat_completions endpoint must also apply the same guardrail logic as invocations (load_guardrails, run BEFORE/AFTER guardrails, respect bypass header, sanitization rewriting).

Type: Function
Name: send_request
Location: mlflow/gateway/guardrails.py
Description: Async function used internally by JudgeGuardrail to make HTTP requests to a sanitization endpoint. Patched in tests as mlflow.gateway.guardrails.send_request. This function must be defined at module level in mlflow/gateway/guardrails.py so the JudgeGuardrail sanitization logic calls it by that path.

Type: Class Method (existing, used by JudgeGuardrail.from_entity)
Name: Scorer.model_validate
Location: mlflow/genai/scorers/base.py
Description: Existing class method used to deserialize a scorer from its serialized representation. JudgeGuardrail.from_entity must use Scorer.model_validate to convert the serialized_scorer data from a ScorerVersion entity into a callable scorer object. Integration tests patch this as mlflow.genai.scorers.base.Scorer.model_validate.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.