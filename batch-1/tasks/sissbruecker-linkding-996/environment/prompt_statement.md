I'm adding browser-extension support to linkding so tools like SingleFile that capture a page locally can push the finished HTML straight to the server instead of making us re-capture it. Right now there's no API for that, the server always runs its own capture, which is wasteful and means extension users can't store their local snapshots at all. So I want a new authenticated API endpoint that takes a URL plus an uploaded HTML file, finds or creates the bookmark for the current user, and stores the file as a compressed snapshot asset. If it has to create the bookmark during this, it should NOT kick off a server-side capture (we already have the snapshot in hand). And if either the url or the html file param is missing, return a clear error saying so.

While I'm in here, I want to pull the snapshot and file-upload stuff out of the general bookmarks service into its own dedicated assets service module so snapshot creation, snapshot upload, and plain file upload all live in one place. Wire the new endpoint through that.

Related: on the bookmark creation path (`@bookmarks/services/bookmarks.py` or wherever create lives) I need a flag to skip scheduling the automatic HTML snapshot, since the caller's about to supply one immediately.

Also the file upload action in the bookmark details view is currently hidden unless the server-side snapshot feature flag is on, which makes no sense, uploads have nothing to do with server-side capturing. So the upload action should always show for the bookmark owner, but the "create snapshot" action should stay gated behind that feature flag.

Oh and one small thing, the user profile API response should include the current application version too.
