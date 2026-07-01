## Description

The Admin API's document retrieval endpoint does not enforce that the requesting client's credentials belong to the same project as the one whose documents are being accessed. This means a client authenticated with credentials from Project A can make a request for documents belonging to Project B simply by supplying Project B's name in the request — and the server will process the request without any access check.

This is a security vulnerability in a multi-tenant system. Each project's secret key should only grant access to that project's own data.

## Expected Behavior

- When a client authenticates using one project's secret key and requests a document by specifying a different project's name, the server should reject the request with an "unauthorized" response (HTTP 401).
- Clients should only be able to retrieve documents from the project whose credentials they are using to authenticate.

## Why This Matters

In a multi-tenant environment, project isolation is critical. Without this check, any project owner could read documents from any other project, compromising data confidentiality. This fix ensures that API credentials are properly scoped to their own project.
