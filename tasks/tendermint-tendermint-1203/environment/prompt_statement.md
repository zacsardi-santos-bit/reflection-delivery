I'm working on making the Tendermint private validator more modular and secure. Right now, the validator is a single monolithic component that bundles key management, signing-state tracking, and persistence together, and it can only run inside the main node process. I'd like to refactor it so that the concerns are separated and, importantly, so that signing can be delegated to an external process over a network socket.

Here's what I need:

The JSON-based validator (the one that reads an unencrypted key from a file) should be moved into its own package with functions to generate a new validator, load one from a file, or load-or-generate. It should still persist to disk after every signing operation and must track the last signed height, round, and step to prevent double signing — rejecting any attempt to sign something that would regress or conflict. When a vote or proposal is re-submitted at the same height/round/step but with a different timestamp, the validator should silently restore the original timestamp and reuse the original signature instead of failing.

In addition, I need a socket-based client and server. The server wraps an existing validator and handles incoming signing requests over TCP. The client connects to the server and proxies all signing operations (votes, proposals, heartbeats) as well as address and public key retrieval. If the client can't connect after all retry attempts are exhausted, it should return a specific "max retries exceeded" error. There should also be a way to configure the connection timeout on the client.

Finally, the time format string used when generating canonical sign bytes needs to be exported so it can be used from the new subpackage.
