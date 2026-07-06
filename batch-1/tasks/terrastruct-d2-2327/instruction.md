Fix the logic for positioning diagram titles with 'near: top-center' so that they are placed above all diagram content, including connection routes that extend beyond shape boundaries. Ensure this behavior is consistent across all layout engines, particularly ELK and Dagre.

*   Update the title placement logic to consider the full bounding box of all diagram content:
    *   Include connection routes (edge paths) in the bounding box calculation.
    *   Ensure edge route points are included in the min/max coordinate calculation, except for edges whose source or destination nodes are inside a near container.
*   Ensure compatibility with both Dagre and ELK layout engines:
    *   Position the title with 'near: top-center' above all content, avoiding overlap with edges or shapes.
    *   Verify that titles are placed at a negative y-coordinate in the rendered board JSON for both layout engines, indicating correct placement above diagram content.
*   Test with diagrams containing:
    *   Nested containers with child nodes.
    *   Circular connections with custom styles (e.g., red stroke and fill).
    *   Ensure no title collision with connection routes in both Dagre and ELK layouts.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.