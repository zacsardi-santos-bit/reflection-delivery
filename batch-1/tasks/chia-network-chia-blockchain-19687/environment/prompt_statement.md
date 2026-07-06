I'm working on the Chia blockchain codebase and have two issues to address.

First, there's a module organization problem: the class that stores fee estimation data lives inside the full-node module, but it's really a shared, protocol-level concept. Any other part of the system that needs to use fee store functionality has to import from the full-node module, which creates awkward coupling. The class should be moved to the protocols module so it lives in the right place and is importable from there.

Second, there's a bug in how the mempool handles non-canonical CLVM encodings in coin solutions. When a spend bundle contains a coin spend that is eligible for deduplication, but that spend's solution program uses a non-canonical encoding, the mempool manager should explicitly reject it and report back an "invalid coin solution" error. Right now it doesn't return the correct error code in this case, so callers can't tell why the spend was refused.
