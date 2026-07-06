## Description

The autotagging system in beets has a design issue: when a match is found between library items and database metadata, the logic to apply that metadata lives in a standalone function rather than on the match objects themselves. This makes it awkward to use — callers must collect the match info and item mapping and pass them to an external function. It also means the match objects don't encapsulate the behavior that's most closely tied to them.

This should be fixed by moving the metadata-application logic directly onto the match objects (both album-level and single-track matches). Each match object is constructed with the item-to-track mapping, and should expose a method to apply its stored metadata to those items.

## Expected Behavior

- Album match objects and single-track match objects each expose a method to apply their match metadata to the associated library items.
- The metadata application method supports all relevant import configuration options, including the "start from scratch" mode that clears all existing tags, the "overwrite with null" setting that controls whether empty values from the new match should blank out existing data, and the "use original release date" flag.
- A utility function for keeping singular and plural artist field variants in sync (e.g. the primary artist name vs. the full list of artists) is expanded to cover more field types (artist sort names, artist credits, artist IDs) and correctly handles edge cases such as case-sensitive matching and featured-artist combined credits.
- The single-value and list-value variants of artist metadata fields must be reconciled when metadata is applied: if the primary artist name is absent but the list is populated, the primary name should be derived from the list; if the list is empty but the primary name is set, the list should be populated from the primary name; and the primary name should always appear at the front of the list.

## Why This Matters

Consolidating metadata application onto match objects makes the API cleaner and more maintainable. It also ensures that all import configuration options (including newer ones like original-date preference and field-level null overwriting) are consistently handled in one place rather than spread across call sites.
