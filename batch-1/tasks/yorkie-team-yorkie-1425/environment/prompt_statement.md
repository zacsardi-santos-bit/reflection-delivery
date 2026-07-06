I've noticed a security issue in our Admin API: when a client authenticates using one project's secret key, they can still retrieve documents from a completely different project just by specifying that other project's name in the request body. There's no check to make sure the authenticated project matches the project being accessed.

In a multi-tenant setup, each project's credentials should only grant access to that project's own documents. Right now, that isolation isn't enforced at all — one project's key can read another project's data, which is a serious access control gap.

I'd like the document retrieval endpoint to validate that the project associated with the provided credentials matches the project whose documents are being requested. If they don't match, the server should respond with an unauthorized error (HTTP 401) instead of serving the data.
