## Description

The router service currently has no way to look up what route corresponds to a given URL without actually navigating to it. Developers sometimes need to inspect route metadata — the route name, URL parameters, query parameters, and even loaded model data — for an arbitrary URL without triggering a full transition and re-render of the application.

## Expected Behavior

- A synchronous method on the router service that accepts a URL string and returns structured route information (route name, local name, parent/child relationship, dynamic params, query params, and param names), or nothing if the URL does not match any route.
- An asynchronous method on the router service that accepts a URL string, loads the matched route's model, and resolves with the same structured route information plus the loaded model data — without navigating to the route.
- Neither method should cause any transition, change the active URL, or trigger re-rendering.
- If the provided URL does not start with the application's configured root URL, both methods should raise an informative error indicating the required URL prefix.
- If the URL is not recognized by the router, the asynchronous method should reject with a clear message identifying the unrecognized URL.
- If a route's model loading fails, the asynchronous method should reject with the underlying error.

## Why This Matters

This capability enables use cases like breadcrumb generation, prefetching, link validation, navigation tree construction, and other custom routing logic where a developer needs to know what a URL resolves to — including its model data — independently of actually navigating there.
