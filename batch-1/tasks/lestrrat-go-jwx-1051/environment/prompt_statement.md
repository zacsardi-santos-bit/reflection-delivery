I'm working with a JWS library and I've found two issues I need to fix.

First, there's a panic happening when the library tries to parse a JWS message in JSON format that is missing its protected header field. Instead of crashing, I'd like the library to gracefully handle such inputs — parsing them successfully even if they can't ultimately be verified. The compact, dot-separated format should still reject messages with empty header segments, but for the JSON serialization format, a missing protected header shouldn't cause a panic.

Second, when verification fails I can't tell why it failed. I need a way to distinguish between errors that are specifically due to the cryptographic verification step failing versus errors caused by something else, like a malformed message structure or a failure elsewhere in the process. I'd like a helper that returns whether an error is specifically a verification error, so I can handle these cases differently in my code.
