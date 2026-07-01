I'm working on the Kubeflow Pipelines frontend and I'd like to add support for displaying artifact links in the run details side panel. Right now, when you click on a node in a pipeline run graph, the side panel shows input and output parameters, but there's no way to see the artifacts that the node consumed or produced.

I need a new component that takes an artifact's storage configuration and renders it as a clickable link. It should handle both S3 and Minio storage backends, constructing the correct link URL based on the endpoint. If the artifact config is invalid — null, undefined, or missing required fields like bucket or key — it should render nothing.

I also need a new method on the workflow parser to extract a node's input and output artifacts, similar to the existing parameters method but for artifacts. As part of this change, the parameters method should be updated to return a named object with distinct input and output fields instead of a positional array, so it's consistent with the new artifacts method.

Finally, the data table component needs to support an optional custom value renderer so that non-string values (like artifact configs) can be displayed using a custom component — in this case, the new artifact link component — rather than just converted to text.
