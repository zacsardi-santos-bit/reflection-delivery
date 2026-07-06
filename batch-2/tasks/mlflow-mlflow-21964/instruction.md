I'm working on adding a content guardrail layer to the MLflow AI Gateway.

*   The JudgeGuardrail class must accept a scorer callable, a stage (BEFORE or AFTER), an action (VALIDATION or SANITIZATION), a name string, and optional action_llm_url and action_endpoint_name parameters.

*   When stage is BEFORE, process_request must invoke the scorer; when stage is AFTER, process_request must return the request unchanged without invoking the scorer.

*   When stage is AFTER, process_response must invoke the scorer; when stage is BEFORE, process_response must return the response unchanged without invoking the scorer.

*   For VALIDATION action: if the scorer result is passing, return the input (request or response) unchanged; if the scorer result is failing, raise GuardrailViolation with a message that includes the guard name and the scorer's rationale.

*   For SANITIZATION action: if the scorer result is passing, return the input unchanged; if the scorer result is failing and action_llm_url is set, call send_request at mlflow.gateway.guardrails.send_request with a payload including a response_format field equal to {"type": "json_schema", "json_schema": {"name": "sanitized_payload", "strict": false, "schema": ChatCompletionRequest.model_json_schema()}} and return the sanitized payload parsed from the LLM response.

*   For SANITIZATION action: if the scorer result is failing and action_llm_url is None (not configured), raise GuardrailViolation with a message that contains 'action_llm_url'.

*   For SANITIZATION action: if send_request returns a response whose message content is not valid JSON, raise GuardrailViolation with 'invalid JSON' in the message.

*   For SANITIZATION action: if send_request raises an HTTPException, raise GuardrailViolation with 'Sanitization request failed' in the message.

*   Scorer return value interpretation: a Feedback object with value True or a case-insensitive 'yes' string is passing; a Feedback object with value False or any string other than 'yes' is failing; a Feedback object with an integer value raises TypeError with 'unexpected value type' in the message.

*   Scorer return value interpretation: raw bool or str scalars follow the same pass/fail semantics as Feedback values; any other raw type (e.g. int) raises TypeError with 'unexpected value type' in the message.

*   Scorer return value interpretation: a list of Feedback objects is passing only if all items are individually passing; if any item fails, raise GuardrailViolation with the guard name and failing item's rationale in the message.

*   JudgeGuardrail.from_entity(entity, server_url=None) must deserialize the scorer by calling mlflow.genai.scorers.Scorer.model_validate(entity.scorer.serialized_scorer) and map entity.stage, entity.action, and entity.name to the guard.

*   JudgeGuardrail.from_entity must set action_endpoint_name and action_llm_url (= server_url) when entity.action_endpoint_name is not None; otherwise action_llm_url must be None.

*   JudgeGuardrail.from_entity must rewrite gateway:/ model URIs on InstructionsJudge scorers: replace the 'gateway:/' prefix with 'openai:/' in scorer.model and set scorer._base_url to '{server_url}/gateway/mlflow/v1/chat/completions'.

*   JudgeGuardrail.from_entity must leave InstructionsJudge scorers unchanged when their model URI does not start with 'gateway:/'.


*   Interface details: Type: Class
Name: GuardrailViolation
Location: mlflow/gateway/guardrails.py
Description: Exception raised when a guardrail blocks a request or response. The error message must contain the guardrail name and the scorer's rationale (e.g., matching pattern "name.*rationale"). Raised with the message "blocked" when no specific rationale is available.

Type: Class
Name: JudgeGuardrail
Location: mlflow/gateway/guardrails.py
Description: Guardrail that uses a scorer function to validate or sanitize requests and responses flowing through the MLflow AI Gateway.
Signature: __init__(self, scorer: Any, stage: GuardrailStage, action: GuardrailAction, name: str, action_llm_url: str = None, action_endpoint_name: str = None)
Attributes:
  - scorer: the scorer callable
  - stage: GuardrailStage value (BEFORE or AFTER)
  - action: GuardrailAction value (VALIDATION or SANITIZATION)
  - name: str identifier for the guardrail
  - action_llm_url: optional URL for the sanitization LLM endpoint
  - action_endpoint_name: optional name of the sanitization endpoint
Methods:
  - async process_request(request: dict) -> dict
  - async process_response(request: dict, response: dict) -> dict
  - @classmethod from_entity(entity: Any, server_url: str = None) -> JudgeGuardrail

Type: Enum
Name: GuardrailStage
Location: mlflow/entities/gateway_guardrail.py
Description: Enum defining when a guardrail runs in the pipeline.
Values: BEFORE, AFTER

Type: Enum
Name: GuardrailAction
Location: mlflow/entities/gateway_guardrail.py
Description: Enum defining what action a guardrail takes when content is flagged.
Values: VALIDATION, SANITIZATION

---

## Method Behaviors

### process_request(request: dict) -> dict
- If `stage == GuardrailStage.BEFORE`: invoke the scorer and apply the configured action.
  - VALIDATION: if scorer result is passing, return `request` unchanged; if failing, raise `GuardrailViolation` containing the guard name and rationale.
  - SANITIZATION: if scorer result is passing, return `request` unchanged; if failing, call `send_request` (imported into `mlflow.gateway.guardrails`) with a payload that includes `response_format` = `{"type": "json_schema", "json_schema": {"name": "sanitized_payload", "strict": False, "schema": ChatCompletionRequest.model_json_schema()}}` and return the sanitized request dict; if `action_llm_url` is None, raise `GuardrailViolation` with "action_llm_url" in the message.
- If `stage == GuardrailStage.AFTER`: return `request` unchanged without calling the scorer.

### process_response(request: dict, response: dict) -> dict
- If `stage == GuardrailStage.AFTER`: invoke the scorer and apply the configured action (same validation/sanitization logic as above but applied to `response`).
- If `stage == GuardrailStage.BEFORE`: return `response` unchanged without calling the scorer.

### Scorer result interpretation
The scorer callable may return:
- `Feedback` object: passing if `value` is `True` or a case-insensitive match to `"yes"`; failing otherwise; raises `TypeError` with "unexpected value type" if value is an integer or other unsupported type.
- `list[Feedback]`: all items must be passing; if any item fails, raises `GuardrailViolation`.
- Raw `bool` or `str`: same pass/fail semantics as `Feedback` values.
- Any other type (e.g., `int`): raises `TypeError` with "unexpected value type".

### Sanitization error handling
- If `send_request` returns a response whose content is not valid JSON: raise `GuardrailViolation` with "invalid JSON" in the message.
- If `send_request` raises an `HTTPException`: raise `GuardrailViolation` with "Sanitization request failed" in the message.

### from_entity(entity, server_url=None) -> JudgeGuardrail
- Reconstructs a `JudgeGuardrail` from a gateway entity object.
- Calls `mlflow.genai.scorers.Scorer.model_validate(entity.scorer.serialized_scorer)` to deserialize the scorer.
- Maps `entity.stage`, `entity.action`, `entity.name` to the guard.
- If `entity.action_endpoint_name` is not None: sets `action_endpoint_name=entity.action_endpoint_name` and `action_llm_url=server_url`.
- If `entity.action_endpoint_name` is None: `action_llm_url` is None.
- If the reconstructed scorer is an instance of `mlflow.genai.judges.instructions_judge.InstructionsJudge` and `scorer.model` starts with `"gateway:/"`:
  - Rewrites `scorer.model` by replacing the `"gateway:/"` prefix with `"openai:/"` (keeping the rest of the URI identical).
  - Sets `scorer._base_url = f"{server_url}/gateway/mlflow/v1/chat/completions"`.
- If the scorer is an `InstructionsJudge` but `scorer.model` does NOT start with `"gateway:/"`: leaves the scorer object unchanged.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.