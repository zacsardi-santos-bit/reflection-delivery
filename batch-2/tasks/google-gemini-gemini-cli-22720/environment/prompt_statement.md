I'm adding tier-aware model handling to the CLI and right now it's kind of broken for non-premium users. If someone doesn't have premium access they can still end up with a premium auto-selected model configured, and when they open the model selection dialog they get shown premium options plus an "Auto" choice they literally can't use, which is confusing.

So two things. First, when the auth flow finishes and detects a user lacks premium access (this comes through an experiment flag), and their currently active model is an auto-selected premium model, I want it to automatically fall back to an appropriate flash model. For pro/premium users the active model should stay unchanged, don't touch it.

Second, the model selection dialog needs updating. When a user without premium access opens it, it should go straight to the manual model list instead of the main view, and that list should only include the models they actually have access to, in this order: flash preview first, then a new flash lite preview variant, then the regular flash options. No "Auto" option for these folks. Oh and when they hit Escape the dialog should just close instead of bouncing back to a main view.

Also I need to introduce that new Flash Lite Preview model. It should only show up for free-tier users in the manual selection view, premium-tier users shouldn't see it in their list at all. It also needs to be recognized as an active model and have a proper display string so it renders right everywhere.

Basically the goal is that the interface and the active model always match what the user's tier actually gives them access to.
