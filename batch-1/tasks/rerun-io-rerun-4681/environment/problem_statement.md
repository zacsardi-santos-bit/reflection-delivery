## Description

The spatial visualization code for 3D point clouds currently ties data retrieval directly to a specific query abstraction. This means the rendering pipeline cannot easily support different query strategies (e.g. cached vs. uncached) without duplicating code or sacrificing structure.

We need to introduce an intermediate data container that separates the component slices used for rendering from the query mechanism that fetches them. This container should hold borrowed references to all the relevant component data (positions, colors, radii, labels, keypoint ids, class ids, and instance keys) and be usable as a bridge between any query backend and the shared rendering logic.

## Expected Behavior

- A new data container type for 3D point cloud visualization components should be defined within the visualizers module.
- The type should hold borrowed slices for all components needed to render a batch of 3D points.
- It should be publicly visible from the visualizers module so that benchmarks and other subsystems can construct it directly.

## Why This Matters

Without this abstraction, it is not possible to benchmark the rendering pipeline independently, nor to support multiple query strategies (such as primary caching) through the same code path. Making the data container public enables benchmarks to measure the cost of actual rendering work rather than query overhead, and allows the visualizer to be driven from cached and uncached paths alike.
