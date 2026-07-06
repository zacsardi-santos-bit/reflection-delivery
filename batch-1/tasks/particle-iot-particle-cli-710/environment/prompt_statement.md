I'm working on the CLI tool for managing cloud logic and I've noticed a few issues that need to be fixed together.

First, there's inconsistent capitalization throughout all the user-facing text. "Logic Functions" is a proper product name, but in help descriptions, error messages, and interactive prompts it appears in all lowercase as "logic function" or "logic functions". This needs to be updated everywhere to consistently use the capitalized form as a proper noun.

Second, when running or deploying a Logic Function, the tool currently prints a static message to standard output immediately before the operation starts. I'd like this replaced with an animated busy spinner that stays active while the operation is in progress, giving users better visual feedback. The method for showing this kind of spinner is already available on the UI object — it accepts a display message and a promise, and resolves the promise while showing the animation.

Third, there's a bug where deploying a Logic Function doesn't correctly capture the function's ID and version from the cloud API response. The API now returns the function's metadata nested inside a wrapper field in the response body, but the current code tries to read the ID and version directly from the top level of the response. This means after a deploy, the function's ID and version are wrong or missing.
