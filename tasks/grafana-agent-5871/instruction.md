Implement a mechanism to allow the addition of extra sidecar containers to the Grafana Agent Helm chart through Helm values. Ensure these containers can share volumes with the main agent container and validate the implementation with a CI test values file.

*   Update `values.yaml`:
    *   Define a new key `controller.extraContainers` as an empty list `[]` by default.
*   Modify the controller pod template (`_pod.yaml`):
    *   Render each entry in `controller.extraContainers` as additional containers in the pod spec.
    *   Ensure these containers are appended after the main agent container and the config-reloader container.
*   Create a CI values file at `operations/helm/charts/grafana-agent/ci/sidecars-values.yaml`:
    *   Configure a GeoIP updater sidecar container with:
        *   Image: `ghcr.io/maxmind/geoipupdate:v6.0`
        *   Name: `geo-ip`
        *   Environment variables: `GEOIPUPDATE_ACCOUNT_ID`, `GEOIPUPDATE_LICENSE_KEY`, `GEOIPUPDATE_EDITION_IDS`, `GEOIPUPDATE_DB_DIR`
        *   Volume mount at `/etc/geoip` with name `geoip`
    *   Configure `agent.mounts.extra` with a volume mount entry:
        *   Name: `geoip`, mountPath: `/etc/geoip`
    *   Configure `controller.volumes.extra` with an entry:
        *   Name: `geoip`, mountPath: `/etc/geoip`
*   Ensure the DaemonSet output matches the golden file at `operations/helm/tests/sidecars/grafana-agent/templates/controllers/daemonset.yaml` when rendered with `sidecars-values.yaml`.
*   Ensure ConfigMap, RBAC (ClusterRole and ClusterRoleBinding), Service, and ServiceAccount outputs match their respective golden files in `operations/helm/tests/sidecars/grafana-agent/templates/`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.