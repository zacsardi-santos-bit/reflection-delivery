## Description

The current load balancing implementations (round-robin, weighted round-robin, weighted random, sticky, and gradual weight ramp-up) are deeply embedded within the client endpoint selection package and can only be used with a specific endpoint type. This makes it impossible to reuse these algorithms in other parts of the system — for example, in the xDS integration layer or any higher-level component that has its own type of candidates — without going through the entire endpoint selection framework.

We need a general-purpose, type-agnostic load balancing module that packages these algorithms as reusable building blocks. The module should expose a clean factory interface that lets callers create any of the supported strategies by supplying a list of candidates and, where needed, simple functions to extract weights or compute hash values from request context.

Additionally, the existing slow-start (weight ramp-up) behavior has a correctness issue: when a new candidate is being ramped up, its reported weight is modified during the ramp-up period. This means that after the ramp-up phase a caller querying the candidate's weight may receive a different value than the one originally configured. The ramp-up logic should track its internal effective weight separately and leave the original candidate weight unmodified.

## Expected Behavior

- A single factory class in a shared package provides static factory methods for round-robin, weighted round-robin, weighted random, sticky, and ramp-up load balancers.
- Each factory method accepts a list of typed candidates and, where needed, a weight-extraction or hash function — no coupling to any particular candidate type.
- The ramp-up load balancer accepts an initial candidate list at construction time and exposes an explicit method for updating candidates, rather than relying on a side-channel from an endpoint group.
- After the ramp-up period is complete, the candidate's reported weight is the same as the originally configured weight — the ramp-up logic does not modify the candidate object's weight.
- Builders for the new load balancer classes appear in the framework's builder return-type registry.

## Why This Matters

Sharing load balancing logic across subsystems reduces duplication and makes it straightforward to compose new endpoint group implementations (such as the xDS-backed groups) without reimplementing selection algorithms. Preserving original candidate weights avoids subtle bugs where downstream code observes a modified weight after the ramp-up period has ended.
