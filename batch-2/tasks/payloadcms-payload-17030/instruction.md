I'm running into a data-loss bug with collections that use cloud storage together with draft versioning.

*   When a collection has cloud storage (local storage disabled) and draft versioning enabled, calling an update operation with the draft flag and a new file must NOT change the main (published) document's status — the main document must remain published with its original filename and data.

*   When a collection has cloud storage and draft versioning enabled, calling an update operation with the draft flag and a new file must create a separate draft version with the new file's filename, the draft status, and updated field data.

*   When saving a draft update that includes a new file on a cloud storage collection, the cloud storage adapter's delete handler must NOT be invoked for the previously published file's filename — the published file must be preserved in cloud storage.

*   When a draft document (with a different file than the published document) is subsequently published, the resulting published document must carry the draft's file and field data — the filename must match what was stored in the draft version.

*   When an update is performed without the draft flag (a normal non-draft update) with a new file on a collection using cloud storage and drafts, the main document must be updated with the new file's filename and the new field data.


*   Interface details: Type: Constant
Name: draftWithUploadCloudStorageCollectionSlug
Location: test/versions/slugs.ts
Signature: export const draftWithUploadCloudStorageCollectionSlug = 'draft-with-upload-cloud-storage'
Description: The slug string for the cloud-storage upload collection used in draft versioning tests.

Type: Variable
Name: cloudStorageDeletedFilenames
Location: test/versions/collections/DraftsWithUploadCloudStorage.ts
Signature: export const cloudStorageDeletedFilenames: string[]
Description: A mutable array that tracks filenames passed to the cloud storage adapter's delete handler. Used to verify that published files are not deleted when saving a draft.

Type: Function
Name: mockCloudStorageAdapter
Location: test/versions/collections/DraftsWithUploadCloudStorage.ts
Signature: mockCloudStorageAdapter() => { name: string; handleDelete: ({ filename }: { filename: string }) => Promise<void>; handleUpload: ({ data }: { data: unknown }) => unknown; staticHandler: () => Response }
Description: Factory function returning a mock cloud storage adapter object. Its handleDelete implementation pushes the given filename into cloudStorageDeletedFilenames.

Type: CollectionConfig
Name: DraftsWithUploadCloudStorage
Location: test/versions/collections/DraftsWithUploadCloudStorage.ts
Description: A Payload collection configuration with slug 'draft-with-upload-cloud-storage', an 'alt' text field, upload enabled with disableLocalStorage set to true, and drafts versioning enabled (drafts: true).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.