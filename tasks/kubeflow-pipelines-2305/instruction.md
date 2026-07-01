Implement support for displaying artifact links in the pipeline run details side panel of the Kubeflow Pipelines frontend. Create a new component for rendering artifact links and update existing methods and components to handle artifacts alongside parameters.

*   Update `WorkflowParser`:
    *   Modify `getNodeInputOutputParams` in `frontend/src/lib/WorkflowParser.ts` to return an object with fields `inputParams` and `outputParams`, each an array of [name, value] pairs. Return `{ inputParams: [], outputParams: [] }` for missing or invalid inputs.
    *   Add `getNodeInputOutputArtifacts` as a new static method in `frontend/src/lib/WorkflowParser.ts`. It should accept optional `workflow` and `nodeId` parameters and return an object with `inputArtifacts` and `outputArtifacts`, each an array of [name, S3Artifact | undefined] pairs. Return `{ inputArtifacts: [], outputArtifacts: [] }` for missing, undefined, empty, or invalid inputs. If an artifact lacks S3 config, set the second element to undefined.

*   Create `MinioArtifactLink` component:
    *   Implement `MinioArtifactLink` in `frontend/src/components/MinioArtifactLink.tsx`. It should be a React functional component that accepts an `S3Artifact` object.
    *   Return `null` if the artifact is null, undefined, an empty object, or missing 'key' or 'bucket'.
    *   Render a clickable anchor element for valid artifacts. For endpoint 's3.amazonaws.com', set `href` to `'artifacts/get?source=s3&bucket={bucket}&key={encodedKey}'` and text to `'s3://{bucket}/{key}'`. For other endpoints, set `href` to `'artifacts/get?source=minio&bucket={bucket}&key={encodedKey}'` and text to `'minio://{bucket}/{key}'`. Ensure the link opens in a new tab with `rel='noreferrer noopener'`.

*   Update `DetailsTable` component:
    *   Modify `DetailsTable` in `frontend/src/components/DetailsTable.tsx` to accept an optional `valueComponent` prop of type `React.FC<S3Artifact>`.
    *   Update the `fields` prop type to accept arrays where the value can be either a string or an `S3Artifact` object.
    *   When `valueComponent` is provided and the field value is a non-null object, render it using `valueComponent` instead of displaying it as text.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.