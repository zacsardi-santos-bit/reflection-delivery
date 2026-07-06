I'm working on an API server for a package registry. Right now there's no way for an authenticated user to list the tokens on their account — I'd like to add an endpoint that returns all their active tokens so they can see what session types are currently authorized. Each token should include at least a type field that distinguishes between web sessions, device flow tokens, and personal access tokens (a new type I also need to introduce).

On a related note, there's a database helper that finds a token by its hash value, but it's just called something generic that doesn't describe what it searches by. I need to rename it to make it explicit that it looks up by hash, which will avoid confusion once I also need a lookup-by-ID version.

Both the rename and the new listing endpoint need to be wired up correctly so the existing token authentication flow keeps working and the new listing route is reachable.
