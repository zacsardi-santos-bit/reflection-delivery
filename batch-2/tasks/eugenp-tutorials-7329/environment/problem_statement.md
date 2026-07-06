I'm on a Spring reactive project and I keep copy-pasting the same little filter logic every time I spin up a `WebClient`, so I want one central utility class with static factory methods that each hand back an `ExchangeFilterFunction` I can plug into the client builder. There's no single home for these cross-cutting behaviors right now and it's getting duplicated all over.

Three filters I need. First, a URL versioning one that takes a version string and appends it as an extra path segment on every outgoing request, so a request to something like "/api/resource" sent through the filter with version "1.0" actually goes out to "/api/resource/1.0". Just tack it on as another segment.

Second, a GET-counting filter that takes some shared counter and bumps it whenever the request method is GET, and leaves it completely alone for anything else like POST. Handy for monitoring request volume.

Third, a logging filter that takes an output stream at construction time and writes a short line about each outgoing request to it. The format is exactly the word "Sending request" then a space then the HTTP method then a space then the full URL, all on one line, and no trailing newline at the end, so don't println it.

Each of these is a static factory returning a filter, all living together so it's easy to compose several onto one client. This is the usual stuff (versioned APIs, request counting, debugging outbound traffic), just consolidated so behavior stays consistent and there's less boilerplate.
