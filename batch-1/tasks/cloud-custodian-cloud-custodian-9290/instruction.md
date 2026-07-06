Implement enhancements to the AWS App Mesh integration in Cloud Custodian to address issues with filtering and reporting. Ensure that mesh policies can filter on spec-level attributes and that reporting output correctly includes all necessary fields, even when nested.

*   Update the `AppmeshMesh` class in `c7n/resources/appmesh.py`:
    *   Ensure the full mesh specification is merged into each resource object before tag augmentation in pull mode.
    *   Follow the API call sequence: list meshes, describe each mesh, then fetch tags.
    *   In event mode, ensure the sequence is: list meshes, describe the event-matching mesh, then fetch tags.
    *   Define `resource_type` with:
        *   id = 'meshName'
        *   name = 'meshName'
        *   arn = 'arn'
        *   date = 'createdAt'
        *   enum_spec = ('list_meshes', 'meshes', None)
        *   detail_spec = ('describe_mesh', 'meshName', 'meshName', 'mesh')

*   Update the `AppmeshVirtualGateway` class in `c7n/resources/appmesh.py`:
    *   Define `resource_type` with:
        *   id = 'metadata.arn'
        *   name = 'virtualGatewayName'
        *   date = 'metadata.createdAt'

*   Enhance the `Formatter` class in `c7n/reports/csvout.py`:
    *   Support dot-path notation for resource field names in `headers()` and `to_csv()` methods.
    *   Ensure `headers()` returns dot-path strings as-is.
    *   Traverse nested structures to extract correct values for dot-path fields in `to_csv()`.

*   Implement the `get_path` function in `c7n/utils.py`:
    *   Use signature: `get_path(path: str, resource: dict) -> any`.
    *   Use jmespath search if the path contains a dot; otherwise, use direct dictionary key lookup.

*   Ensure ARN derivation logic in resource managers supports resolving ARNs from dot-path field names.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.