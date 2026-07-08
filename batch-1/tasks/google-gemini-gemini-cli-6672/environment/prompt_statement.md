I've got this editor settings hook in the CLI UI layer that manages editor preference stuff, and right now it's pulling its config settings straight out of a shared React context. Problem is every test that touches this hook has to stand up a full context provider wrapper just to hand it the settings, which is annoying coupling and makes the thing hard to reuse or test in isolation.

What I want is to refactor the hook so it takes the loaded settings object as its first argument instead of reading it from context. That way callers, and tests especially, can just pass in a mock settings object directly when they call the hook without wrapping anything in a provider.

It should keep doing everything it does now though. So it still tracks whether the editor selection dialog is open, and it opens and closes that dialog. It handles editor selection too, which means persisting the chosen preference, validating whether the editor is actually available or allowed in the current environment (like sandbox-restricted cases where an editor isn't permitted), and clearing the preference when needed. Oh and it should still surface errors properly if saving the preference fails, don't swallow those.

Basically just make the settings dependency explicit as a param, no behavior changes otherwise.
