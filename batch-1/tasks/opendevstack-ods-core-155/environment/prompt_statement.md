I'm working on a webhook proxy service that takes incoming build requests and forwards them to an orchestration system, and right now the error handling has some real gaps I need to close up.

First thing, if the incoming request body is malformed and can't be parsed as JSON, I want it to immediately return HTTP 400 with a message saying the JSON couldn't be parsed, instead of silently swallowing the parse error and proceeding with incomplete data (which just leads to confusing downstream failures later).

Second, even when the body parses fine as JSON, the payload can still be semantically invalid, like an empty branch name or other required fields being blank. In that case I don't want it forwarding a bogus event downstream, I want a 400 back with a message that says the input is invalid.

Third one's the tricky bit, when the downstream orchestration system rejects the pipeline creation (say it returns a 422), that status code needs to actually propagate back to the original caller along with a clear error message about the pipeline not being created. Right now the error just gets swallowed and the caller gets nothing useful, so operators can't tell a bad request apart from a validation failure apart from a downstream rejection. To make this work the pipeline creation function's signature needs to change so it hands back a status code alongside any error, that way the handler can propagate the right status.

And finally, the trigger secret handling. Requests missing a valid secret (absent, empty, or just plain wrong) should keep getting rejected with 401, but the important part is no event processing happens at all in those cases, so the pipeline creation logic should never even be invoked, not just that we return the 401 after the fact.

Basically I want each stage (bad JSON, invalid input, downstream reject, missing auth) to report predictably so clients get real feedback and debugging isn't a guessing game.
