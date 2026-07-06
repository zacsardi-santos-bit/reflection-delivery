I'm working on the Prefect Kubernetes integration and want to add better visibility into pod failures and infrastructure state.

*   The diagnose_k8s_pod function must return None for healthy or non-actionable pod states: an empty status dict, a Pending pod with no container statuses, a Running pod with normally running containers, and a Succeeded pod with a container terminated with exitCode 0 and reason 'Completed'.

*   When a container's waiting state has reason 'ImagePullBackOff' or 'ErrImagePull', diagnose_k8s_pod must return an InfrastructureDiagnosis with level ERROR, the container name in summary, the reason string in detail, and the word 'image' (case-insensitive) in resolution. This must work even when the waiting state has no 'message' field.

*   When a container's terminated state has reason 'OOMKilled', diagnose_k8s_pod must return an InfrastructureDiagnosis with level ERROR, both 'OOMKilled' and the container name in summary, and the word 'memory' (case-insensitive) in resolution.

*   When a container's waiting state has reason 'CrashLoopBackOff', diagnose_k8s_pod must return an InfrastructureDiagnosis with level ERROR, 'crash-looping' in summary, and the word 'logs' (case-insensitive) in resolution.

*   When the pod's conditions list contains a PodScheduled condition with reason 'Unschedulable', diagnose_k8s_pod must return an InfrastructureDiagnosis with level WARNING, 'unschedulable' (case-insensitive) in summary, and the condition's message string in detail. A PodScheduled condition with reason 'Scheduled' must NOT produce a diagnosis. This must work even without a 'message' field on the condition.

*   When the pod-level status dict has reason 'Evicted', diagnose_k8s_pod must return an InfrastructureDiagnosis with level WARNING, 'evicted' (case-insensitive) in summary, and the pod's message (if present) in detail. This must work even without a 'message' field.

*   When a container's terminated state has reason 'Evicted', diagnose_k8s_pod must return an InfrastructureDiagnosis with level WARNING and 'evicted' (case-insensitive) in summary.

*   diagnose_k8s_pod must check both 'initContainerStatuses' and 'containerStatuses' when scanning container failure conditions. The container name from initContainerStatuses must appear in the returned diagnosis summary.

*   When a pod has multiple containers, waiting-state failures (ImagePullBackOff, ErrImagePull, CrashLoopBackOff) must take priority over terminated-state failures (OOMKilled) in the returned diagnosis.

*   InfrastructureDiagnosis must be a frozen dataclass: attempting to assign to any of its fields after instantiation must raise AttributeError. Two InfrastructureDiagnosis instances with identical level, summary, detail, and resolution must compare as equal.

*   When _replicate_pod_event processes a Pending-phase pod event and the associated flow run is in a Scheduled (initial pending) state, it must propose a state with name 'InfrastructurePending' and a message containing the word 'pending' (case-insensitive).

*   When _replicate_pod_event processes a Pending-phase pod event, the InfrastructurePending state must NOT be proposed if the flow run is already in any of these states: Running, final (Completed, Crashed), Paused/Suspended, Cancelling, or already named 'InfrastructurePending'.

*   When _replicate_pod_event processes a Pending-phase pod event and the flow run cannot be found (ObjectNotFound), the InfrastructurePending state proposal must be silently skipped without error.

*   When _replicate_pod_event processes a Running-phase pod event, no InfrastructurePending state proposal must be made.

*   When _replicate_pod_event detects a pod failure via diagnose_k8s_pod, it must emit the log by: (1) calling flow_run_logger(flow_run_id=<uuid>) from prefect.logging.loggers, (2) calling .getChild('observer') on the result, and (3) calling .log() on that child logger with the level as the first positional arg (logging.ERROR for ERROR-level diagnoses, logging.WARNING for WARNING-level), a printf-style format string as the second arg, and the message parts (including summary and detail) as subsequent positional args.

*   Diagnosis logs must be deduplicated using _last_diagnosis_cache keyed by pod UID: if the same diagnosis is returned for the same pod UID on consecutive events, the log must only be emitted once. When the pod returns to a healthy state (diagnose_k8s_pod returns None), the cache entry for that UID must be cleared, so that a subsequent recurrence of the same failure IS logged again.

*   Healthy pods (diagnose_k8s_pod returns None) must not trigger any flow_run_logger call.


*   Interface details: Type: Enum
Name: DiagnosisLevel
Location: src/integrations/prefect-kubernetes/prefect_kubernetes/diagnostics.py
Description: Severity level for an infrastructure diagnosis. Must have at least ERROR and WARNING values (string enum: ERROR = "error", WARNING = "warning").

Type: Class
Name: InfrastructureDiagnosis
Location: src/integrations/prefect-kubernetes/prefect_kubernetes/diagnostics.py
Description: A frozen dataclass representing a structured diagnosis of a Kubernetes pod failure. Must be frozen (setting attributes after creation raises AttributeError). Two instances with identical field values must compare as equal.
Fields:
  level: DiagnosisLevel
  summary: str
  detail: str
  resolution: str

Type: Function
Name: diagnose_k8s_pod
Location: src/integrations/prefect-kubernetes/prefect_kubernetes/diagnostics.py
Signature: diagnose_k8s_pod(status: dict[str, Any]) -> InfrastructureDiagnosis | None
Description: Inspects a pod status dictionary and returns a structured diagnosis for known failure conditions, or None if the pod is healthy or in a state not requiring intervention.

Type: Variable
Name: _last_diagnosis_cache
Location: src/integrations/prefect-kubernetes/prefect_kubernetes/observer.py
Description: Module-level TTLCache (from cachetools) keyed by pod UID (str) mapping to InfrastructureDiagnosis. Used for deduplication of diagnosis log messages. Must support .clear() and .pop(key, default) operations. Tests import it directly as `from prefect_kubernetes.observer import _last_diagnosis_cache`.

Notes on _replicate_pod_event logging behavior (required by tests):
- The flow run logger must be obtained by calling flow_run_logger (imported from prefect.logging.loggers) with keyword argument flow_run_id, i.e.: flow_run_logger(flow_run_id=<uuid>)
- A child logger must be obtained by calling .getChild("observer") on the result of flow_run_logger(...)
- The .log() method must be called on that child logger with: the log level as first positional arg, a printf-style format string as second positional arg, and the message parts as subsequent positional args — NOT a pre-formatted f-string


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.