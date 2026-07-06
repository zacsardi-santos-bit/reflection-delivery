Implement an option in the metadata table component to display nested object values in their original YAML format. Ensure that Kubernetes resource drawers utilize this option to show nested properties with their original names, while still allowing top-level keys to be formatted if a title formatter is configured.

*   Update the `StructuredMetadataTable` component:
    *   Add a new optional boolean field `nestedValuesAsYaml` to the `options` prop.
    *   When `nestedValuesAsYaml` is true, render nested object or array values as a YAML code block, preserving original camelCase property keys.
    *   Ensure that if a custom `titleFormat` is provided, it applies only to top-level metadata keys, not affecting nested YAML content.
    *   Ensure array values within metadata appear embedded inside the YAML block.

*   Modify the `KubernetesStructuredMetadataTableDrawer` component:
    *   Pass `nestedValuesAsYaml: true` in the options to `StructuredMetadataTable`.
    *   Ensure this change affects `DeploymentDrawer`, `StatefulSetDrawer`, and other drawers using this component to render nested fields as YAML.

*   Update the `IngressesAccordions` component:
    *   Pass `nestedValuesAsYaml: true` in the options to `StructuredMetadataTable`.
    *   Render nested ingress rule properties as YAML: 'host', 'servicePort', and 'serviceName'.

*   Update the `ServicesAccordions` component:
    *   Pass `nestedValuesAsYaml: true` in the options to `StructuredMetadataTable`.
    *   Render nested service properties as YAML: 'targetPort' and 'app'.

*   Ensure the following specific renderings in the respective drawers:
    *   Deployment drawer: Render 'rollingUpdate:', 'maxSurge: 25%', 'maxUnavailable: 25%', and 'type: RollingUpdate'.
    *   Ingress drawer: Render 'host: api.awesome-host.io', 'servicePort: 80', and 'serviceName: awesome-service'.
    *   Service drawer: Render 'targetPort: 1997' and 'app: awesome-service'.
    *   Stateful set drawer: Render 'type: RollingUpdate', 'rollingUpdate:', 'maxSurge: 25%', 'maxUnavailable: 25%', 'matchLabels:', and 'app: dice-roller'.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.