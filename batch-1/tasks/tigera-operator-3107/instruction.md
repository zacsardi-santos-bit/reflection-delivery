Update the RBAC permissions for the 'calico-prometheus-operator' to include new resource types required by the latest version of the monitoring API. Ensure the cluster role is correctly expanded to handle these new resources and adjust verb ordering for consistency.

*   Modify the 'calico-prometheus-operator' ClusterRole to define exactly 10 policy rules.
*   Update the first policy rule (index 0) to cover the 'monitoring.coreos.com' API group with the following resources:
    *   'alertmanagers'
    *   'alertmanagers/finalizers'
    *   'alertmanagers/status'
    *   'alertmanagerconfigs'
    *   'prometheuses'
    *   'prometheuses/finalizers'
    *   'prometheuses/status'
    *   'prometheusagents'
    *   'prometheusagents/finalizers'
    *   'prometheusagents/status'
    *   'thanosrulers'
    *   'thanosrulers/finalizers'
    *   'thanosrulers/status'
    *   'scrapeconfigs'
    *   'servicemonitors'
    *   'podmonitors'
    *   'probes'
*   Ensure the policy rule for 'services' and 'endpoints' resources lists verbs in this order:
    *   'get'
    *   'create'
    *   'update'
    *   'delete'
*   Add a new policy rule at index 8 for the 'storage.k8s.io' API group, granting 'get' on 'storageclasses'.
*   Shift the existing policy rule for 'podsecuritypolicies' (API group 'policy', verb 'use') to index 9.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.