## Description

There is a bug in the quadrilateral point-containment check used throughout the canvas renderer. When a quadrilateral shape is positioned at relatively large coordinate values (e.g., several hundred units from the canvas origin), the containment test incorrectly reports that interior points are *outside* the shape.

For example, a rectangular quad spanning from coordinates (300, 300) to (500, 500) should contain the point (350, 350), but the current implementation returns false for this case.

## Expected Behavior

- A point that visually lies inside a quadrilateral should always be reported as inside, regardless of whether the quadrilateral's coordinates are small (near the origin) or large.
- The containment test should give consistent, correct results across all areas of the canvas.

## Why This Matters

Features that depend on containment testing — such as selection hit-testing and region-based interaction — silently break when canvas elements are placed at moderate-to-large positions. This means users may be unable to select or interact with objects that are placed anywhere other than near the top-left of the canvas.
