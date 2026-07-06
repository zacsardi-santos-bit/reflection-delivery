Implement a new, generic load balancing module in the `com.linecorp.armeria.common.loadbalancer` package. This module should provide reusable load balancing strategies that are type-agnostic and can be used across different parts of the system. Ensure the module includes a factory class with static methods for creating various load balancer instances, and address the correctness issue in the existing ramp-up implementation.

Requirements:

*   Create a `LoadBalancer` factory class with static methods:
    *   `ofRoundRobin(List<T> candidates)`
    *   `ofWeightedRoundRobin(List<T> candidates)`
    *   `ofWeightedRoundRobin(List<T> candidates, ToIntFunction<T> weightFunction)`
    *   `ofWeightedRandom(List<T> candidates, ToIntFunction<T> weightFunction)`
    *   `ofSticky(List<T> candidates, ToLongFunction<Ctx> hasher)`
    *   `builderForRampingUp(List<T> initialCandidates)`

*   Implement `SimpleLoadBalancer<T>` for:
    *   Round-robin selection: cycle through candidates, return `null` if empty.
    *   Weighted round-robin: use smooth weighted round-robin, handle equal weights as round-robin.
    *   Weighted random: select candidates randomly proportional to weights, return `null` if all weights are 0.

*   Implement `WeightedRandomLoadBalancer<T>`:
    *   Provide `entries()` method returning `CandidateContext` objects with `get()` and `counter()` methods.

*   Implement `LoadBalancer<T, Ctx>` for sticky routing:
    *   Ensure consistent routing of the same context value to the same candidate.

*   Implement `RampingUpLoadBalancerBuilder<T>` with methods:
    *   `weightTransition(WeightTransition<T>)`
    *   `rampingUpInterval(Duration)`
    *   `rampingUpTaskWindow(Duration)`
    *   `totalSteps(int)`
    *   `ticker(LongSupplier)`
    *   `timestampFunction(Function<T, Long>)`
    *   `executor(EventExecutor)`
    *   `build()`

*   Implement `RampingUpLoadBalancer<T>`:
    *   `updateCandidates(List<T> candidates)`
    *   `close()`
    *   `windowIndex(long nanos)`
    *   `weightedRandomLoadBalancer()`
    *   `rampingUpWindowsMap` field with `candidateAndSteps()` method.

*   Implement `RampingUpLoadBalancer.CandidateAndStep<T>`:
    *   Constructor with `(T candidate, ToIntFunction<T> weightFunction, WeightTransition<T> weightTransition, int step, int totalSteps)`
    *   Methods: `candidate()`, `step()`

*   Implement `WeightTransition<T>` interface:
    *   Static methods: `linear()`, `aggression(double aggression, double minWeightPercent)`
    *   `compute(T candidate, int weight, int currentStep, int totalSteps)`

*   Ensure `RampingUpLoadBalancer` does not modify original candidate weights.
    *   Use `WeightedObject<T>` to track ramped-up weights internally.

*   Register `RampingUpLoadBalancerBuilder` in the framework's builder return-type allowlist.

*   Ensure `EndpointSelectionStrategy.builderForRampingUp()` remains functional.

*   Update `WeightedRoundRobinStrategyTest.select` to start with the weight-1 endpoint in the pick sequence.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.