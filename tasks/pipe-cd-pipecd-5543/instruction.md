Update the deployment platform to support multiple deployment plugins by organizing deploy targets as a map from plugin names to their respective target lists. Modify all relevant interfaces and implementations to reflect this new structure.

*   Change the `UpdateDeployTargets` method on the `ApplicationStore` interface:
    *   Update the third parameter from a flat list of strings to a map with keys as plugin names and values as lists of deploy targets using `structpb.ListValue`.
*   Modify the mock implementation in `pkg/datastore/datastoretest/datastore.mock.go`:
    *   Use `deployTargetsByPlugin` as the parameter name for the third argument.
    *   Ensure the type is `map[string]*structpb.ListValue`.
    *   Import the `structpb` package with the alias `structpb` from `google.golang.org/protobuf/types/known/structpb`.
*   Update the `apiApplicationStore` interface in `pkg/app/server/grpcapi/api.go`:
    *   Declare `UpdateDeployTargets` with the new map-based signature to ensure compatibility with the updated mock and datastore interface.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.