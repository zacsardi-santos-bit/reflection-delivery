Implement a new data container struct for 3D point cloud visualization components to decouple rendering from query mechanisms. Ensure this struct is publicly accessible and can hold borrowed slices of component data.

*   Define the `Points3DComponentData` struct in `crates/re_space_view_spatial/src/visualizers/points3d.rs`.
    *   Make it generic over at least one lifetime parameter to hold borrowed slices.
    *   Include fields for positions, colors, radii, labels, keypoint ids, class ids, and instance keys.
*   Ensure `Points3DComponentData` is publicly accessible from the visualizers module.
    *   Re-export the struct in `crates/re_space_view_spatial/src/visualizers/mod.rs` using `pub use points3d::Points3DComponentData;`.
*   Update existing rendering methods to accept `Points3DComponentData` instead of raw query results.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.