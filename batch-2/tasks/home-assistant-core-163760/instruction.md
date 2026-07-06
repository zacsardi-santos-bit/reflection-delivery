I'm working on the Roborock vacuum integration in Home Assistant and I need to add support for segment-based cleaning across multiple floor maps.

*   The Roborock vacuum entity must support the standard vacuum 'clean area' feature, enabling the clean area service to be called on the entity.

*   The vacuum entity must implement a method to retrieve all available room segments across all loaded floor maps. Each segment must be represented with an id (in the format '{map_flag}:{room_segment_id}' where both parts are integers), a name (the room name), and a group (the map/floor name). When no map data is available, the method must return an empty list.

*   A WebSocket command of type 'vacuum/get_segments' must be registered. It must accept an entity_id parameter and return a JSON response with a 'segments' key containing the list of segment objects: each with 'id', 'name', and 'group' string fields.

*   The vacuum entity must implement a method to clean specified segments. This method accepts a list of segment ID strings in '{map_flag}:{segment_id}' format. Both parts of each segment ID must be parseable as integers; if either part is not an integer or the colon-separated format is missing, a service validation error must be raised with message matching 'Invalid segment ID format: {segment_id}'.

*   When all segments in a clean request belong to the same map as the currently active map, the segment clean command must be sent directly without switching maps. The segment clean command must be sent with integer segment IDs (the second part of the '{map_flag}:{segment_id}' format) as a list under a 'segments' key.

*   When segments belong to a different floor map than the currently active one, the implementation must switch to that target map before issuing the clean command.

*   If a clean request includes segments from more than one floor map, a service validation error must be raised with a message matching 'All segments must belong to the same map'.

*   If switching to the target floor map fails (due to a device communication error), a service validation error must be raised with a message matching 'Error while calling load_multi_map'.

*   The Roborock strings.json file must include translation entries for the error conditions: a 'multiple_maps_in_clean' key with message 'All segments must belong to the same map. Got segments from maps: {map_flags}', a 'segment_id_parse_error' key with message 'Invalid segment ID format: {segment_id}', and the existing 'command_failed' translation key used with placeholder 'load_multi_map' to produce the map-switch error message.


*   Interface details: Type: Class
Name: RoborockVacuum
Location: homeassistant/components/roborock/vacuum.py
Description: The Roborock vacuum entity class. Must include VacuumEntityFeature.CLEAN_AREA in its supported features bitmask. Must implement the following methods:
Signature: async_get_segments(self) -> list[Segment]
Signature: async_clean_segments(self, segment_ids: list[str], **kwargs: Any) -> None

---

Type: WebSocket Command
Name: vacuum/get_segments
Location: homeassistant/components/vacuum/ (registered via the vacuum component's WebSocket API)
Description: Handles WebSocket messages of type "vacuum/get_segments". Accepts an "entity_id" field. Calls async_get_segments on the target vacuum entity and returns the result as {"segments": [{"id": str, "name": str, "group": str}, ...]}.

---

Type: Translation Strings
Name: strings.json entries
Location: homeassistant/components/roborock/strings.json
Description: Two new error translation keys must be added under the "exceptions" section:
  - "multiple_maps_in_clean" with message: "All segments must belong to the same map. Got segments from maps: {map_flags}"
  - "segment_id_parse_error" with message: "Invalid segment ID format: {segment_id}"
The existing "command_failed" translation key (used with placeholder "load_multi_map") produces the map-switch error message "Error while calling load_multi_map".


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.