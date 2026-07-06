Restructure the event delivery system in the collaborative document library to consolidate changes from multiple containers into a single event object. Ensure that document-level metadata is clearly separated from container-specific data, and update the TypeScript event type to reflect these changes.

* Update the Rust `DiffEvent` struct:
    * Replace the `doc` field with an `event_meta` field for document-level metadata.
    * Replace the `container` field with an `events` field, which is a collection of per-container diff items.
    * Ensure `event_meta` includes `from_checkout: bool` and `local: bool` flags.
    * Ensure `events` is an ordered collection of `ContainerDiff` items, each with a `diff` and `path`.

* Update the TypeScript event type:
    * Rename `LoroEvent` to `LoroEventBatch` in `loro-js/src/index.ts`.
    * Ensure `LoroEventBatch` includes:
        * `local: boolean` and `fromCheckout: boolean` for document-level metadata.
        * An `events` array of `LoroEvent` entries, each with `target`, `diff`, and `path`.
    * Ensure `LoroEvent` represents a single container's diff within `LoroEventBatch.events`.

* Ensure subscribers can:
    * Access document-level metadata via `event.event_meta` in Rust.
    * Access per-container diffs via the `events` array in both Rust and TypeScript.
    * Iterate over all container-level diffs in a single callback invocation.

* Update all TypeScript imports and references:
    * Change any imports of `LoroEvent` to `LoroEventBatch`.
    * Use `LoroEventBatch` as the callback event type.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.