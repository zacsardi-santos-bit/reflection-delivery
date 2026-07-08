I'm cleaning up the request data utilities in the Sentry JavaScript SDK core package and could use a hand. Right now a couple of helpers, the cookie parsing one and the client IP extraction one, are stuck inside some internal utils module that was never meant to be their long-term home, so I want them moved out to cleaner, more findable spots in the package. That's the reorg part.

The bigger piece is a brand new module of request utilities for pulling normalized request data out of HTTP requests. Thing is I've got two request shapes to deal with: traditional Node.js server style requests, and modern edge runtime (WinterCG-compatible) ones. For both I want to grab headers as a plain string-to-string dictionary, and any header that's multi-value (an array) should just get filtered out. I also need the full URL, query string params, plus cookies and body data when they're present.

For the Node HTTP side specifically, if all I've got is a relative path and a host header, reconstruct the full URL from that, and do protocol detection too, including the case where the socket's encryption state (TLS) tells me it's actually https. The WinterCG extraction should cover headers, method, URL, and query string.

Oh and I want a standalone helper that just pulls the query string portion out of any URL string, returning undefined if there's no query params on it. The header conversion should handle both the WinterCG-style headers and plain-object formats, filtering arrays in both.

The reason I care: the SDK has to work across Node servers, edge runtimes, and frameworks with weird non-standard request shapes, and consistent normalization here keeps error reports carrying complete, accurate request context.
