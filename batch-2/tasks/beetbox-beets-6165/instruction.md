I'm working on refactoring the autotagging system in beets so that match result objects own their own metadata-application logic.

*   AlbumMatch must accept (Distance, AlbumInfo, dict) as constructor arguments, where the dict maps item objects to their matched TrackInfo objects. The old constructor signature that accepted extra_items and extra_tracks as required positional set arguments is no longer valid; those parameters should be optional (defaulting to empty lists).

*   AlbumMatch must expose an apply_metadata() method (no arguments) that applies album and track metadata to all items in the mapping stored at construction time, respecting config options: per_disc_numbering, artist_credit, original_date, and import.from_scratch.

*   TrackMatch must accept (Distance, TrackInfo, Item) as constructor arguments and expose an apply_metadata() method (no arguments) that applies the TrackInfo metadata to the stored item, respecting config options including import.from_scratch and overwrite_null.

*   When the import.from_scratch config option is True, both AlbumMatch.apply_metadata() and TrackMatch.apply_metadata() must clear all existing item metadata (including fields not covered by the new match info, such as 'comments') before applying new values.

*   When a field name is listed in overwrite_null.album (for AlbumMatch) or overwrite_null.track (for TrackMatch) config, apply_metadata() must overwrite the item's existing value for that field even when the match info provides None. When a field is NOT listed in overwrite_null, a None value from the match info must leave the item's existing value unchanged.

*   When original_date config is True and original_year/original_month/original_day are set on the AlbumInfo, apply_metadata() must assign those original date values to the item's year/month/day fields instead of the release year/month/day.

*   correct_list_fields must be importable from beets.autotag.hooks (in addition to any existing exports from beets.autotag). It must accept a plain dict and return a new dict — it must NOT mutate an Item in place as the previous version did.

*   correct_list_fields must reconcile these singular/plural field pairs: (albumtype, albumtypes), (artist, artists), (artist_credit, artists_credit), (artist_id, artists_ids), (artist_sort, artists_sort).

*   correct_list_fields reconciliation rules: (1) If single is None/empty and list is empty, return both unchanged. (2) If single is None/empty and list is non-empty, set single = list[0]. (3) If single is non-None and list is empty, set list = [single]. (4) If single is non-None and an exact (case-sensitive) match of single exists in the list, move it to index 0 (deduplicated). (5) If single is non-None, no exact match exists in the list, but at least one whitespace-split word of the single value (compared case-insensitively) matches any complete list item (compared case-insensitively), return both unchanged. (6) Otherwise (no exact or word-level overlap match), prepend single to the front of the list.

*   Example correct_list_fields inputs and expected outputs: (None, []) → (None, []); (None, ['1']) → ('1', ['1']); ('1', []) → ('1', ['1']); ('1', ['2', '1']) → ('1', ['1', '2']); ('1', ['2']) → ('1', ['1', '2']); ('1 ft 2', ['1', '1 ft 2']) → ('1 ft 2', ['1 ft 2', '1']); ('1 FT 2', ['1', '1 ft 2']) → ('1 FT 2', ['1', '1 ft 2']) [unchanged because 'ft' and '1' are words of '1 FT 2' that appear in the list]; ('a', ['b', 'A']) → ('a', ['b', 'A']) [unchanged because 'a' lowercased matches 'A' lowercased]; ('1 ft 2', ['2', '1']) → ('1 ft 2', ['2', '1']) [unchanged because '1' and '2' are words of '1 ft 2' that appear in the list].

*   When applying album match metadata, if a track in the album match has its own non-None artist, the item's 'artists' field must be derived from the track-level artist info rather than defaulting to the album-level artists list.

*   When applying album match metadata, if the album info's artist_sort is None but artists_sort is a non-empty list, the item's albumartist_sort must be set to artists_sort[0] (the first element of the sort-name list), not an empty string.

*   When applying album match metadata, the item's albumartists_credit field must be reconciled via correct_list_fields so that the album's single artist_credit value appears at the front of the artists_credit list when it is not already present.

*   AlbumInfo, TrackInfo, AlbumMatch, TrackMatch, and correct_list_fields must all be importable directly from beets.autotag.hooks.

*   The Distance class in beets/autotag/distance.py must be instantiatable with no arguments (Distance()) for use as the first argument to AlbumMatch and TrackMatch constructors.


*   Interface details: Type: Class
Name: AlbumMatch
Location: beets/autotag/hooks.py
Description: Represents a match between a set of items and an AlbumInfo, encapsulating the item-to-track mapping and providing metadata application logic. The constructor signature is AlbumMatch(distance: Distance, info: AlbumInfo, mapping: dict) where mapping is {item: track_info, ...}. The trailing set parameters (extra_items, extra_tracks) are removed. Exposes an apply_metadata() method that applies the album and track metadata to all items in the mapping, respecting config options: per_disc_numbering, artist_credit, original_date, and import.from_scratch.
Signature: AlbumMatch(distance: Distance, info: AlbumInfo, mapping: dict)
          apply_metadata() -> None

Type: Class
Name: TrackMatch
Location: beets/autotag/hooks.py
Description: Represents a match between a single item and a TrackInfo. Provides an apply_metadata() method that applies the track info metadata to the item, respecting config options including import.from_scratch and overwrite_null.
Signature: TrackMatch(distance: Distance, info: TrackInfo, item: Item)
          apply_metadata() -> None

Type: Function
Name: correct_list_fields
Location: beets/autotag/hooks.py
Description: Reconciles singular and plural field variants in a metadata dict. Takes a dict and returns a new dict with the following field pairs made consistent: (albumtype, albumtypes), (artist, artists), (artist_credit, artists_credit), (artist_id, artists_ids), (artist_sort, artists_sort). Previously mutated an Item in-place; now takes a plain dict and returns a new dict.
Signature: correct_list_fields(data: dict) -> dict

Type: Class
Name: Distance
Location: beets/autotag/distance.py
Description: Represents a match distance/score. Must be instantiatable with no arguments: Distance(). Used as the first argument to AlbumMatch and TrackMatch constructors.
Signature: Distance()


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.