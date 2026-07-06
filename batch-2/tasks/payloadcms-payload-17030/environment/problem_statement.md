## Description

When using a collection that combines cloud storage (with local file storage disabled) and draft versioning, saving a draft that includes a new file upload causes two serious bugs:

1. The currently published version of the document gets overwritten or its status changed — editors lose their published content.
2. The cloud storage provider receives a delete request for the published file even though it's still needed — the published file disappears from the cloud.

## Expected Behavior

- Saving a draft with a new file should leave the published document completely untouched: same status, same filename, same field values.
- The published file in cloud storage must not be deleted when a new draft file is uploaded.
- The draft document should have its own distinct file and data, separate from the published version.
- When the draft is subsequently published, the published document should be updated to use the draft's file and data.
- Normal (non-draft) updates with a new file should continue to update the main document as expected.

## Why This Matters

Content editors using cloud storage backends rely on the draft/publish workflow to stage changes before they go live. The current behavior corrupts the published state and deletes live files as a side effect of saving a draft — this is a data-loss bug. A working implementation must keep the published document and its cloud file intact while a draft with a new file is being prepared.
