## Description

The GoFr command-line framework currently has no built-in support for interactive terminal feedback during long-running operations. Developers building CLI tools with GoFr have no standard way to display live progress indicators — animated spinners or progress bars — while background work is happening. This forces each developer to roll their own terminal UI code, leading to inconsistency and duplicated effort.

## Expected Behavior

- A terminal output utility should be available within the framework's command package, providing ANSI escape sequence helpers for cursor movement, screen control, and line management.
- An animated spinner component should be available in multiple visual styles. The spinner must run asynchronously and stop cleanly when the associated operation context is cancelled.
- A progress bar component should be available that displays percentage-based progress inline in the terminal, adapting its display based on the detected terminal width (showing only a percentage on narrow terminals, and a full ASCII bar on wider ones).
- Command handlers in the framework should have access to the terminal output helper through the request context, so handlers can use spinners and progress bars naturally.
- Example CLI subcommands should demonstrate how to integrate these components, including correct handling of context cancellation.

## Why This Matters

Without these components, developers must implement terminal animation and progress reporting from scratch each time. Standardizing this within the framework improves developer experience, ensures consistent behavior across CLI applications, and makes it easier to build user-friendly command-line tools on top of GoFr.
