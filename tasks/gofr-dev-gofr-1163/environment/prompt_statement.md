I'm building a CLI application using the GoFr framework and I need to add interactive terminal feedback for long-running operations. Right now there's no built-in way to show users that something is happening — no spinner, no progress bar, nothing. I'd like to add a terminal output package that provides animated spinners in multiple visual styles and a percentage-based progress bar that adapts to the terminal width.

The spinner should run asynchronously and stop automatically when the operation's context is cancelled. The progress bar should show a visual ASCII bar on wide terminals and fall back to a plain percentage on narrow ones — and it should also stop cleanly on context cancellation.

I also need the command context that gets passed to handlers to carry the terminal output instance, so handler functions can use these components naturally without having to create their own output streams. The framework's command runner should be initialized with this output as well.

To demonstrate these features, I'd like to add two example subcommands — one that shows a spinner while doing work and prints a completion message, and one that shows a progress bar stepping through several percentages and then prints a completion message. Both should handle context cancellation gracefully by returning the appropriate cancellation error.
