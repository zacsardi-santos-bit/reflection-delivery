I'm hitting a few rough edges in the recipe installer inside our CLI tool and want to clean them up. The main install routine is private right now, which means I can't call it from outside the package or write decent integration tests that run the whole flow, so I want it exposed as a public method that external callers and tests can invoke directly without poking at internals.

Also the install status tracking is annoying. If I want to know whether recipe X reached status Y I have to manually scan the whole statuses list, so I'd like a direct query method that takes a recipe name and a desired status and just tells me whether that recipe hit it.

Another thing, when someone runs install with auto-acceptance (an "assume yes" mode where confirmation prompts get automatically accepted), that setting needs to actually propagate into the recipe execution context so the prompts get bypassed. Right now I'm not confident it does.

Oh and I need a clean data structure that represents the outcome of recipe detection, basically pairing a recipe definition with its detected status, so the install pipeline can work with detection results directly instead of going through the old bundle abstraction.

The flow should also clearly split guided mode (installing everything discovered) from targeted mode (installing one specific recipe by name), and it's gotta accurately track each recipe through its lifecycle stages: detected, available, installing, installed, failed, recommended, skipped, cancelled, and unsupported, with those counts reflecting what really happened during a run. This is all about making the installer usable and testable end to end instead of forcing consumers to work around implementation details.
