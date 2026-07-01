I'm working on a monorepo build tool and I'd like to add support for building based on the current local working directory state, rather than only operating on committed git history. Right now, if I make changes to a module and haven't committed yet, I can't use the tool to build just the modules I've been working on — I have to commit first.

What I'd like is a way to create a manifest from the local directory that detects which modules have uncommitted changes or are newly added. When no changes are present, the resulting module list should be empty. When I've modified files in a module, that module should show up. When I've added a completely new module that's not tracked by git yet, that should also be picked up and marked as a local module. There should also be an option to include all modules regardless of change state, for when I want a full local rebuild.

Additionally, I'd like a corresponding build function that can execute builds using this local manifest, routing stdout and stderr appropriately and firing the standard build-stage lifecycle events.

I also need a utility that retrieves the diff between the current working directory and the index, so that the local change detection works correctly even for modified-but-unstaged files.
