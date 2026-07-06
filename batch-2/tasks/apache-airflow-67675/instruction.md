I'm working on the Airflow Helm chart and I'd like to add support for the Kubernetes Gateway API as an alternative to Ingress for exposing the API server.

*   A new Helm template file must be created at `chart/templates/api-server/api-server-httproute.yaml` that renders a Kubernetes Gateway API HTTPRoute resource for the Airflow API server.

*   The HTTPRoute resource must only be rendered when both `apiServer.enabled` is true AND `httpRoute.apiServer.enabled` is true. If either is false or not set, the template must produce no output.

*   The rendered resource must have `apiVersion: gateway.networking.k8s.io/v1` and `kind: HTTPRoute`.

*   The `metadata.labels` of the rendered resource must always include a `tier` label and a `release` label, and must merge in labels from the top-level `labels` value, `apiServer.labels`, and `httpRoute.apiServer.labels` — with `httpRoute.apiServer.labels` taking highest priority.

*   When `httpRoute.apiServer.annotations` is provided, those key-value pairs must appear verbatim in `metadata.annotations`.

*   When `httpRoute.apiServer.parentRefs` is set, those entries must be passed through to `spec.parentRefs`, including all fields such as `name`, `namespace`, and `sectionName`.

*   When `httpRoute.apiServer.hostnames` is set, those values must appear in `spec.hostnames`. Each hostname must support Helm template expressions (e.g., a hostname containing `{{ .Release.Name }}` must be rendered to its resolved value).

*   By default, the template must generate one rule under `spec.rules` with a path match of type `PathPrefix` and value `/`, and a backendRef pointing to the API server service named `{release-name}-api-server` on port `8080`.

*   When `httpRoute.apiServer.path` and `httpRoute.apiServer.pathType` are set, those values must override the default rule's path match `value` and `type` respectively.

*   When `httpRoute.apiServer.rules` is provided, it must override the entire generated default rule — the provided rules are used verbatim in `spec.rules` and no default rule is generated.

*   The backend service name must respect `fullnameOverride` when `useStandardNaming` is true, producing a backend name of `{fullname-override}-api-server`.

*   The CRD lookup map in `chart/tests/chart_utils/helm_template_generator.py` must include an entry for `gateway.networking.k8s.io/v1::HTTPRoute` pointing to the CRD definition file at `chart/tests/chart_utils/gateway.networking.k8s.io_httproutes.yaml`, so that chart validation can validate HTTPRoute resources.


*   Interface details: Type: HelmTemplate
Name: api-server-httproute.yaml
Location: chart/templates/api-server/api-server-httproute.yaml
Description: New Helm template that renders a Kubernetes Gateway API HTTPRoute resource for the Airflow API server. The template accepts the following values configuration:

- `httpRoute.apiServer.enabled` (bool): When true (and `apiServer.enabled` is also true), the HTTPRoute resource is rendered. When false or absent, no resource is rendered.
- `httpRoute.apiServer.annotations` (map): Key-value pairs added verbatim to `metadata.annotations`.
- `httpRoute.apiServer.labels` (map): Labels merged into `metadata.labels` alongside global `labels` and `apiServer.labels`.
- `httpRoute.apiServer.parentRefs` (list): List of Gateway parent references passed through to `spec.parentRefs`. Each entry may include `name`, `namespace`, `sectionName`, etc.
- `httpRoute.apiServer.hostnames` (list of strings): Domain names set in `spec.hostnames`. Each string supports Helm template expression syntax (rendered via `tpl`).
- `httpRoute.apiServer.path` (string): Path value for the default routing rule's path match. Defaults to `/`.
- `httpRoute.apiServer.pathType` (string): Path match type for the default routing rule (e.g., `PathPrefix`, `Exact`). Defaults to `PathPrefix`.
- `httpRoute.apiServer.rules` (list): When set, fully overrides the generated default routing rule. Passed verbatim to `spec.rules`.

The rendered resource structure:
- `apiVersion`: must be `gateway.networking.k8s.io/v1`
- `kind`: must be `HTTPRoute`
- `metadata.labels`: must include `tier` and `release` labels, merged with `values.labels`, `values.apiServer.labels`, and `values.httpRoute.apiServer.labels`
- `spec.rules[0].matches[0].path.type`: `PathPrefix` by default (or `httpRoute.apiServer.pathType`)
- `spec.rules[0].matches[0].path.value`: `/` by default (or `httpRoute.apiServer.path`)
- `spec.rules[0].backendRefs[0].name`: `{release-name}-api-server` (respects `fullnameOverride` + `useStandardNaming`)
- `spec.rules[0].backendRefs[0].port`: `8080`

---

Type: ConfigEntry
Name: crd_lookup entry
Location: chart/tests/chart_utils/helm_template_generator.py
Description: The `crd_lookup` dictionary in `helm_template_generator.py` must include a new entry mapping the key `"gateway.networking.k8s.io/v1::HTTPRoute"` to the path of the HTTPRoute CRD YAML file: `f"{MY_DIR.as_posix()}/gateway.networking.k8s.io_httproutes.yaml"`. This allows Helm chart validation to validate HTTPRoute resources against the CRD schema.

---

Type: File
Name: gateway.networking.k8s.io_httproutes.yaml
Location: chart/tests/chart_utils/gateway.networking.k8s.io_httproutes.yaml
Description: The CustomResourceDefinition (CRD) YAML file for the Kubernetes Gateway API HTTPRoute resource (standard channel, bundle version v1.2.1). Referenced by the `crd_lookup` entry above for schema validation during tests.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.