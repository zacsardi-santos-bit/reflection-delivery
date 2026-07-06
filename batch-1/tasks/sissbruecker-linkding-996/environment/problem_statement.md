## Description

Linkding currently can only create HTML snapshots of bookmarked pages by running a server-side capture process. There is no way for a browser extension that has already captured a page to push that snapshot directly to the server. This means users of browser extensions like SingleFile lose the ability to store locally-captured snapshots in linkding without triggering a redundant second capture.

Additionally, the file upload button for bookmarks is currently hidden unless the server-side HTML snapshot feature is enabled — even though uploading a file is completely independent of that feature. Users who want to upload files to their bookmarks cannot do so unless the admin has enabled the server-side snapshot feature.

## Expected Behavior

- A new API endpoint should allow clients (e.g. browser extensions) to POST a pre-captured HTML snapshot, associated with a URL. If the bookmark does not yet exist for the authenticated user, it should be created automatically — without triggering a new server-side capture. The endpoint should require authentication and return an appropriate error when required parameters are missing.
- The file upload action on a bookmark should always be available to the bookmark owner, regardless of whether the server-side HTML snapshot feature is enabled. The "create snapshot" action should only appear when the server-side feature is active.
- Snapshot and file upload logic should live in a dedicated assets service, cleanly separated from the general bookmarks service.
- When creating a bookmark programmatically, it should be possible to opt out of triggering an automatic HTML snapshot.
- The user profile API response should expose the application version.

## Why This Matters

Browser extensions that capture pages locally should be able to send those captures to linkding without duplicating work. Making file uploads available regardless of the snapshot setting removes a confusing limitation and improves the usability of the application.
