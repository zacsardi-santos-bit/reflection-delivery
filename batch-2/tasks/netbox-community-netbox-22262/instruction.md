I'm running into a bug where cables connected via circuit terminations don't show up when I filter cables by site or location.

*   When a CableTermination record's termination is a circuit termination (identified by the model label 'circuits.circuittermination'), cache_related_objects must assign the circuit termination's cached site to self._site and the circuit termination's cached location to self._location.

*   Cables with one or more endpoints connected via circuit terminations must appear in cable filterset results when filtering by location_id or location name, provided the circuit termination is associated with that location.

*   Cables with one or more endpoints connected via circuit terminations must appear in cable filterset results when filtering by site_id or site slug, provided the circuit termination is associated with that site (either directly or through a location).

*   A cable that has both A-side and B-side endpoints as circuit terminations — one associated with a site and one associated with a location — must be returned by both the location filter (matched via the location-associated termination) and the site filter (matched via either the site-associated or location-associated termination).

*   A cable with both A-side and B-side endpoints connected (i.e., fully terminated) via circuit terminations must be excluded from the unterminated=True filter and included in the unterminated=False filter.


*   Interface details: Type: Method
Name: cache_related_objects
Location: netbox/dcim/models/cables.py
Description: Method on the CableTermination model that caches related site and location references onto the CableTermination record. When the termination is a circuit termination, this method must set both self._site (from the circuit termination's own cached site field) and self._location (from the circuit termination's own cached location field). The circuit termination type must be identified using the model's meta label (circuits.circuittermination). Previously the method only partially handled circuit terminations — it used the wrong site attribute and never set the location.
Signature: cache_related_objects(self) -> None


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.