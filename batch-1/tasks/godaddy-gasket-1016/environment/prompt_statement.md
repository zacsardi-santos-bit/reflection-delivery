I'm working with a framework that supports loading plugins dynamically at startup, and I'm running into an issue where dynamically loaded plugins can't register their own CLI commands. The command registration phase runs too early — before the dynamic plugins have been loaded — so by the time my dynamic plugins are available, the window to register commands has already closed.

There's also a secondary issue: dynamically loaded plugins are only being run through part of the initialization sequence. Static plugins go through three setup phases, but dynamic plugins only go through the first two. This means anything my dynamic plugin sets up in the third phase simply never happens.

I'd like the command registration step to be moved to a later phase in the startup lifecycle, so that dynamic plugins have a chance to contribute commands. I also need dynamic plugins to be fully initialized across all three setup phases just like static plugins. When the framework skips re-running a setup phase for a plugin that was already initialized, it would be helpful to emit a trace message noting the skip so developers can follow what happened.

Additionally, I want to be able to reference dynamic plugins using relative file paths (like a path starting with a dot) that are resolved relative to the application's root directory, rather than only supporting package names.
