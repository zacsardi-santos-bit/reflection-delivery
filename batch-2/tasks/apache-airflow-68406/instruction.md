I'm working with the Airflow Helm chart and I need to add support for the Kubernetes Gateway API networking standard for the API server component.

*   A new Helm chart template must exist at `chart/templates/api-server/api-server-httproute.yaml` that renders a Gateway API HTTPRoute resource only when `httpRoute.apiServer.enabled` is `true`; it must not render when `httpRoute.apiServer.enabled` is `false` or absent, and it must not render when `apiServer.enabled` is `false` even if `httpRoute.apiServer.enabled` is `true`.

*   The rendered HTTPRoute resource must have `apiVersion: gateway.networking.k8s.io/v1` and `kind: HTTPRoute`.

*   When `httpRoute.apiServer.annotations` is set (e.g., `{"aa": "bb", "cc": "dd"}`), those key-value pairs must appear in `metadata.annotations` of the rendered resource.

*   The rendered resource's `metadata.labels` must include built-in `tier` and `release` labels as well as any labels from the global `labels` values key, from `apiServer.labels`, and from `httpRoute.apiServer.labels` — all merged together.

*   When `httpRoute.apiServer.parentRefs` is set as a list of objects, those objects must appear verbatim in `spec.parentRefs`, including optional fields such as `sectionName`.

*   When `httpRoute.apiServer.hostnames` is set as a list of strings, those strings must appear in `spec.hostnames`; hostname strings support Helm Go template syntax (e.g., `{{ .Release.Name }}.example.com` must be rendered to the actual release name).

*   By default (no custom path, pathType, or rules provided), the rendered `spec.rules` must contain exactly one rule: `matches[0].path.type` must be `PathPrefix`, `matches[0].path.value` must be `/`, `backendRefs[0].name` must be `{release-name}-api-server`, and `backendRefs[0].port` must be `8080`.

*   When `httpRoute.apiServer.path` is set, `spec.rules[0].matches[0].path.value` must use that value instead of the default `/`.

*   When `httpRoute.apiServer.pathType` is set, `spec.rules[0].matches[0].path.type` must use that value instead of the default `PathPrefix`.

*   When `httpRoute.apiServer.rules` is provided, those rules must entirely replace the default rule in `spec.rules`.

*   When both `fullnameOverride` and `useStandardNaming: true` are set in chart values, the backend service name in `spec.rules[0].backendRefs[0].name` must be `{fullnameOverride}-api-server` rather than the default release-name-based name.

*   The `crd_lookup` dictionary in `helm-tests/tests/chart_utils/helm_template_generator.py` must contain an entry with key `"gateway.networking.k8s.io/v1::HTTPRoute"` mapping to the path of the `gateway.networking.k8s.io_httproutes.yaml` CRD file in the same directory, enabling schema validation of the rendered HTTPRoute resources.


*   Interface details: ## Helm Chart Template

Type: Helm Template File
Name: api-server-httproute.yaml
Location: chart/templates/api-server/api-server-httproute.yaml
Description: A new Helm chart template that renders a Kubernetes Gateway API HTTPRoute resource for the Airflow API server. The file must be located exactly at this path so tests can render it with `show_only=["templates/api-server/api-server-httproute.yaml"]`.

The template must:
- Render only when `httpRoute.apiServer.enabled` is `true` AND `apiServer.enabled` is not `false`
- Produce a resource with `apiVersion: gateway.networking.k8s.io/v1` and `kind: HTTPRoute`
- Set `metadata.annotations` from `httpRoute.apiServer.annotations`
- Set `metadata.labels` to include standard `tier` and `release` labels merged with global `labels`, `apiServer.labels`, and `httpRoute.apiServer.labels`
- Set `spec.parentRefs` from `httpRoute.apiServer.parentRefs` (supporting `name`, `namespace`, `sectionName` fields)
- Set `spec.hostnames` from `httpRoute.apiServer.hostnames` (values must support Helm template syntax such as `{{ .Release.Name }}.example.com`)
- Default `spec.rules` to one rule: `matches[0].path.type = "PathPrefix"`, `matches[0].path.value = "/"`, `backendRefs[0].name = "{release-name}-api-server"`, `backendRefs[0].port = 8080`
- Allow `httpRoute.apiServer.path` to override the path value and `httpRoute.apiServer.pathType` to override the path type in the default rule
- Allow `httpRoute.apiServer.rules` to fully override the default rule
- When `fullnameOverride` and `useStandardNaming: true` are set, the backend ref name must be `{fullnameOverride}-api-server`

## CRD Lookup Entry

Type: Dictionary Entry
Name: crd_lookup
Location: helm-tests/tests/chart_utils/helm_template_generator.py
Description: The `crd_lookup` dictionary in `helm_template_generator.py` must have an entry for the HTTPRoute CRD so it can be validated during chart rendering tests. The key must be `"gateway.networking.k8s.io/v1::HTTPRoute"` and the value must be the path to the CRD YAML file `gateway.networking.k8s.io_httproutes.yaml` in the same `chart_utils` directory.

## CRD Schema File

Type: YAML File
Name: gateway.networking.k8s.io_httproutes.yaml
Location: helm-tests/tests/chart_utils/gateway.networking.k8s.io_httproutes.yaml
Description: A new CustomResourceDefinition YAML file for the `gateway.networking.k8s.io/v1` HTTPRoute resource, used for validation of rendered Helm templates in tests. This file is provided in test.patch and does not need to be implemented — it is already supplied.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.