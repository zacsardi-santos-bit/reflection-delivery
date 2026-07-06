I'm trying to add integration tests to the library checker judge API server, but I'm running into several problems that prevent the tests from passing.

First, the server crashes immediately on startup if an authentication secret isn't set as an environment variable. In my test environment this secret isn't configured, and the server should be able to fall back to a default value and keep running rather than aborting.

Second, when I ask the server for the list of supported programming languages, it comes back empty. The server is looking for the language definitions file in the wrong location — it was moved during a recent directory restructuring, and the path in the code hasn't been updated.

Third, the submission endpoint accepts source code of any size, including very large files. I'd expect the server to reject submissions where the source code exceeds 1 MiB, returning an error instead of processing the request.

Once these three issues are fixed, the server should start cleanly in a test environment, return a non-empty list of supported languages, and properly reject oversized source code submissions with an error.
