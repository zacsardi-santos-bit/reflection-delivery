I'm working on linkding and want to support browser extensions that capture web pages locally and push those snapshots to the server. Right now there's no API endpoint to receive an already-captured HTML snapshot from a client — the server always tries to capture pages itself. I need a new API endpoint that accepts a URL and an HTML file, finds or creates the corresponding bookmark for the authenticated user, and stores the file as a compressed snapshot asset. If the bookmark is created during this process, it should not trigger a separate server-side capture. The endpoint should require authentication and return a clear error if either parameter is missing.

I also want to split snapshot and file-upload logic out of the general bookmarks service into its own dedicated module, so snapshot creation, snapshot upload, and general file upload are all handled in one place.

Additionally, the file upload action in the bookmark details view should always be visible to the bookmark owner — it's currently hidden unless the server-side snapshot feature flag is on, which doesn't make sense for uploads that have nothing to do with server-side capturing. The "create snapshot" action should still be gated by that feature flag, but the upload action should not be.

On the bookmark creation side, I need a way to tell the service not to schedule an automatic HTML snapshot — useful when a snapshot is going to be supplied immediately by the caller.

Finally, the user profile API response should include the current application version.
