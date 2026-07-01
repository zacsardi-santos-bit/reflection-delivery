## Description

The coin management CLI commands (listing, combining, and splitting coins) currently bypass the shared command infrastructure and invoke the wallet RPC layer directly. This means they miss out on consistent behavior that other commands enjoy: wallet synchronization checks, graceful error handling for missing wallets, and unified transaction configuration. We should refactor these commands to use the new class-based command framework so they integrate properly with the shared transaction configuration system.

Additionally, several CLI flags for reading and writing transaction files use short, cryptic single-letter abbreviations. These should be renamed to full descriptive names to improve clarity and consistency with the rest of the CLI.

## Expected Behavior

- The coin listing, combining, and splitting commands should be built on top of the shared transaction command base class, inheriting consistent wallet sync checks and error handling.
- When a wallet ID cannot be found, the command should print a user-friendly message indicating the wallet was not found, rather than crashing.
- When the wallet is not synchronized, the command should print a message asking the user to wait rather than proceeding blindly.
- The transaction file input and output flags should use full descriptive names instead of short single-letter abbreviations.
- New helper classes should be introduced in the command class module so that commands can declaratively express their configuration needs — for coin selection settings, full transaction configuration, and timelock parameters.
- A decorator must be applied to the run method of any command that extends the transaction endpoint base class; failing to do so should raise a clear error at instantiation time.
- The classes for reading and writing transaction files should be moved into the shared command class module so they can be reused across more than just the signer commands.
- Coin listing must support optional pagination (showing a fixed number of coins per page) and displaying unconfirmed coins separately. Each coin's identifier and amount in the smallest unit must appear in the output.

## Why This Matters

Having coin management commands live outside the shared framework creates inconsistencies in user experience — some commands handle sync and missing-wallet errors gracefully while others do not. Bringing everything into the same class-based framework ensures a consistent, predictable CLI interface for users. The flag renaming makes the CLI more approachable for new users who encounter these flags without prior context.
