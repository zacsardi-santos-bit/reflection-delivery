I'm working on the recipe installer in our CLI tool and running into a few design gaps I'd like to address. Right now the main installation routine is private, which makes it hard to call from outside the package or to write clean integration-level tests that exercise the full install flow. I'd like to expose it as a public method.

At the same time, our installation status tracking doesn't have a convenient way to ask "did recipe X reach status Y?" — right now you have to manually scan through the list of statuses. It would be really helpful to have a direct query method for that.

I also want to make sure that when a user runs the install with auto-acceptance enabled, that setting gets properly propagated into the recipe execution context so prompts are bypassed automatically.

Finally, we need a clean data structure to represent the outcome of recipe detection — something that pairs a recipe definition with its detected status — so the installation pipeline can work with detection results directly rather than going through the old bundle abstraction.

The installation flow should also clearly distinguish between guided mode (installing everything discovered) and targeted mode (installing a specific recipe by name), and it should accurately track each recipe's lifecycle events: detection, availability check, installation attempt, success, failure, recommendation, skip, and cancellation.
