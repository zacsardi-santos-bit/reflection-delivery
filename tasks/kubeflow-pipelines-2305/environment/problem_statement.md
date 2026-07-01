## Description

When viewing details for a specific node in a pipeline run, the UI currently shows input and output **parameters** in the side panel, but there is no support for displaying **artifacts** — files or data stored in object storage (such as Minio or S3). Users have no way to see or access the artifacts associated with a node's inputs or outputs.

Additionally, the data table component used throughout the UI only supports string values, making it impossible to render artifact entries as clickable links alongside plain text fields.

## Expected Behavior

- The workflow parser should expose a new method for retrieving a node's input and output artifacts (alongside the existing method for parameters). The artifacts method should follow the same pattern as the parameters method, returning an object with named fields rather than a positional tuple.
- The parameters method should also be updated to return a named object with distinct input and output parameter fields instead of a positional array, for clarity and consistency.
- A new artifact link component should be introduced that, given an artifact's storage configuration, renders a clickable link to retrieve it. The link should correctly distinguish between S3 and Minio storage based on the endpoint. Invalid or incomplete artifact configs (missing bucket or key) should render nothing.
- The data table component should accept an optional custom value renderer so that artifact entries can be displayed as links instead of raw text.

## Why This Matters

Without this feature, users inspecting pipeline node details cannot discover or navigate to artifacts produced or consumed by a node. Supporting artifact links in the run details panel makes the pipeline run UI significantly more useful for debugging and data exploration.
