## Description

The CKB chain crate contains a number of test helper utilities — including mock blockchain builders, transaction factories, and reward calculators — that are defined locally and are therefore unavailable to other crates in the repository. This creates duplication risk: any other crate that needs to write similar tests must re-implement the same helpers from scratch.

These utilities belong in the shared test utilities crate that already exists for this purpose, rather than being hidden inside the chain crate's local test module.

## Expected Behavior

- The shared test utilities crate should export all the common blockchain test helpers: mock chain builders, transaction factory functions, cellbase creators, reward calculators, and DAO field calculators.
- The chain crate's local test helpers module should be slimmed down to only what is truly local to chain tests (i.e., chain controller startup helpers).
- All existing chain tests should continue to pass, now obtaining the shared helpers from the shared utilities crate.
- The shared utilities crate should add whatever dependency it needs to support the newly moved utilities, and the chain crate should drop the dependency that is no longer required there.

## Why This Matters

Centralizing shared test helpers in one place improves code reuse across the project, reduces duplication, and makes it easier for other crates to write consistent tests without copying and maintaining their own versions of the same utilities.
