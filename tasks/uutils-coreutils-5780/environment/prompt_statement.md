I'm running into issues with how trailing slashes are handled in destination paths for the copy and move commands. There are a few cases that don't work correctly.

When I try to copy a regular file to a path like "no-such/" where that path doesn't exist as a directory, I expect to get a clear error telling me that "no-such/" is not a directory. Instead, the behavior is inconsistent or the error message isn't what it should be.

When I recursively copy a directory to a new destination that ends with a slash, using the option that prevents the source from being placed inside the destination rather than renaming to it, the operation should succeed and create the destination directory. Currently it fails when it shouldn't.

For the move command, when I move a directory to a destination ending with a slash, two scenarios should work: if the destination doesn't exist, the source should be renamed to the destination; if the destination already exists as a directory, the source should be moved inside it. Neither case is currently handled correctly.

Can you fix the copy and move commands so that trailing slashes in destination paths are handled properly in all these cases?
