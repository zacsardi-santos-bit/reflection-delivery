Fix the bug in the quadrilateral point-containment logic to ensure that points inside a quadrilateral are correctly identified as contained, regardless of the quadrilateral's position on the canvas.

*   Update the `Quad::contains` method:
    *   Ensure it returns `true` for any point strictly inside the quadrilateral.
    *   Specifically, verify that a `Quad` spanning from (300.0, 300.0) to (500.0, 500.0) contains the point (350.0, 350.0).
    *   Implement the method in `node-graph/gcore/src/graphic_element/renderer.rs` with the signature: `contains(&self, p: DVec2) -> bool`.
    *   Use a ray-casting (winding number) algorithm to determine point containment.
    *   Ensure the method handles quadrilaterals at large coordinate values correctly.

*   Implement the `Quad::from_box` associated function:
    *   Accept an array of two `DVec2` corner points to construct a `Quad`.
    *   Ensure the resulting `Quad` is valid for use with the `contains` method.
    *   Implement the function in `node-graph/gcore/src/graphic_element/renderer.rs` with the signature: `from_box(corners: [DVec2; 2]) -> Quad`.

*   Ensure the `Quad` struct:
    *   Is defined in `node-graph/gcore/src/graphic_element/renderer.rs`.
    *   Supports construction from a bounding box and point-containment testing.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.