Implement support for composition functions to declare and receive additional Kubernetes resources during the composition pipeline. Ensure the composition engine can handle a request-fetch-retry loop to fetch these resources, and integrate this capability into the local rendering tool for development purposes.

*   Implement the `LoadExtraResources` function in `cmd/crank/beta/render/load.go`:
    *   Accept a filesystem abstraction and a file path.
    *   Parse the file as a YAML stream of Kubernetes resource manifests.
    *   Return a slice of unstructured resources or an error if the file cannot be opened or parsed.

*   Update the `Inputs` struct in `cmd/crank/beta/render/render.go`:
    *   Add an `ExtraResources` field of type `[]unstructured.Unstructured`.
    *   Add a `Context` field of type `map[string][]byte`.

*   Implement the `filterExtraResources` function in `cmd/crank/beta/render/render.go`:
    *   Accept a slice of unstructured resources and a `ResourceSelector`.
    *   Return matching resources as `*fnv1beta1.Resources`.
    *   Return `nil, nil` for an empty resources slice or a nil selector.
    *   For `MatchName` selectors, match by `apiVersion`, `kind`, and exact name.
    *   For `MatchLabels` selectors, match by `apiVersion`, `kind`, and label subset.

*   Enhance the `Render` function to support iterative extra-resources loop:
    *   Filter `Inputs.ExtraResources` using selectors from `Requirements`.
    *   Populate the next `RunFunctionRequest`'s `ExtraResources` map.
    *   Re-invoke the function until requirements stabilize or the maximum iterations are reached.
    *   Ensure zero items are received in `ExtraResources` for selectors that match no resources.

*   Define the `ExtraResourcesFetcher` interface in `internal/controller/apiextensions/composite/composition_functions.go`:
    *   Include a `Fetch` method with signature `Fetch(ctx context.Context, rs *v1beta1.ResourceSelector) (*v1beta1.Resources, error)`.

*   Implement `ExtraResourcesFetcherFn` in `internal/controller/apiextensions/composite/composition_functions.go`:
    *   Define it as a function type with the same signature as `ExtraResourcesFetcher.Fetch`.
    *   Implement the `ExtraResourcesFetcher` interface via a `Fetch` method.

*   Create the `WithExtraResourcesFetcher` function in `internal/controller/apiextensions/composite/composition_functions.go`:
    *   Accept an `ExtraResourcesFetcher`.
    *   Configure the `FunctionComposer` to use it for satisfying function requirements.

*   Implement `NewExistingExtraResourcesFetcher` in `internal/controller/apiextensions/composite/composition_functions.go`:
    *   Accept a `client.Reader`.
    *   Return an `*ExistingExtraResourcesFetcher`.
    *   For `MatchName` selector, call `Get` and handle `NotFound` with `nil, nil`.
    *   For `MatchLabels` selector, call `List` and return matching items.

*   Ensure the `testdata/extra-resources.yaml` file exists under `cmd/crank/beta/render/testdata/`:
    *   Contain at least two Kubernetes resource manifests in YAML stream format.
    *   Ensure it can be loaded by `LoadExtraResources`.

*   Define `MaxRequirementsIterations` as an exported constant in `internal/controller/apiextensions/composite/composition_functions.go`:
    *   Set its value to 5 to cap the number of iterations in the extra-resources loop.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.