## Description

When using edge-based contraction hierarchy routing with turn restrictions and via-points (waypoints placed on road segments), the algorithm produces incorrect results. Specifically, placing a query point on the middle of a road creates a virtual connection point internally, and the routing algorithm does not correctly handle U-turn detection at these virtual points. This can cause the router to find paths that should be blocked by turn restrictions, or conversely, to fail to find valid paths that pass through virtual points when no restrictions apply.

## Expected Behavior

- Routing through a via-point (virtual node on a road segment) should succeed and produce a correct path when no restrictions prevent it, including correct distances.
- When turn restrictions make all routes between two points impossible — including routes that pass through virtual nodes — the algorithm should correctly report that no path exists.
- Turning onto a virtual edge that doubles back on the same underlying road (a U-turn through a virtual point) must be treated as a U-turn and blocked accordingly.

## Related Changes

The utility function used in tests to build random graphs also needs to be updated. It currently takes a seed value internally and creates its own random generator. It should instead accept a caller-supplied random generator so that callers can control and share the random state. It should also support setting random edge speeds via an encoded value parameter, with a configurable probability for non-zero loop distances.

## Why This Matters

Incorrect routing through via-points with turn restrictions is a serious correctness bug. Users routing with waypoints near intersections that have turn restrictions could receive routes that are illegal to drive.
