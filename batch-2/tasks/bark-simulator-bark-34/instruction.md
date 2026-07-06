Implement support for lane types in the road simulation framework to enable accurate modeling of urban roads with various lane categories. Decouple lane creation from the plan view to allow geometry offset from any reference line, facilitating the correct positioning of outer lanes.

*   Create a standalone function `create_lane_from_lane_width` in `modules/world/opendrive/lane.hpp`:
    *   Accept parameters: `LanePosition lane_position`, `geometry::Line previous_line`, `LaneWidth lane_width_current`, and optional `float s_inc` (default 0.5f).
    *   Return a pointer to the created lane.
*   Update the `PlanView` class in `modules/world/opendrive/plan_view.hpp`:
    *   Implement `get_reference_line()` method returning the internal reference geometry line.
*   Modify the `Lane` class in `modules/world/opendrive/lane.hpp`:
    *   Add `set_lane_type(const LaneType lt)` and `get_lane_type()` methods.
    *   Ensure lane type defaults to `LaneType::NONE` upon construction.
*   Define a `LaneType` enum in `modules/world/opendrive/commons.hpp` with values: `NONE=0`, `DRIVING=1`, `BIKING=4`, `SIDEWALK=5`, `BORDER=6`.
*   Update Python bindings in `python/world/opendrive.cpp`:
    *   Bind `create_lane_from_lane_width` as a static method on the `Lane` class.
    *   Expose `lane_type` as a read/write property on `Lane` objects, using `Lane::get_lane_type` and `Lane::set_lane_type`.
    *   Define a `LaneType` enum with values accessible as attributes and through `__members__`.
*   Enhance the `LaneSection` class in `modules/world/opendrive/lane_section.hpp`:
    *   Implement `get_lane_by_position(LanePosition pos)` method returning the lane at the specified position.
*   Update the XODR parser:
    *   Process all lane types present in `LaneType.__members__`, setting the `lane_type` property accordingly.
    *   Skip unsupported lane types.
    *   Compute lane geometry offsets relative to adjacent inner lanes using `get_lane_by_position`.
*   Ensure a test OpenDRIVE file exists at `modules/runtime/tests/data/urban_road.xodr` with specified lane types and IDs.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.