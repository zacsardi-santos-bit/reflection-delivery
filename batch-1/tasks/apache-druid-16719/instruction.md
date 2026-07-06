Implement a circular collection class called `CircularList` that supports round-robin iteration and set equality checks. Ensure that the `KillUnusedSegments` coordinator duty uses this class to fairly distribute kill tasks across datasources.

*   Create the `CircularList` class in `org.apache.druid.collections` package.
    *   Implement `Iterable<T>` interface.
    *   Constructor: `CircularList(Set<T> elements, Comparator<? super T> comparator)`
        *   Accepts a `Set<T>` and a `Comparator<? super T>`.
        *   Sort elements using the comparator.
        *   Initialize the internal cursor at position -1.

*   Implement iteration behavior:
    *   When iterating with a natural-order comparator over a set like `{"b", "a", "c"}`, yield elements in ascending order: `["a", "b", "c"]`.
    *   When iterating with a reverse-order comparator over a set like `{-1, 100, 0, -4}`, iterating `2*N` elements (where `N` is the set size) must yield two complete cycles in descending order: `[100, 0, -1, -4, 100, 0, -1, -4]`.
    *   Resume iteration from the last returned element when stopped partway, wrapping around past the end back to the start.

*   Implement `equalsSet` method:
    *   `boolean equalsSet(Set<T> inputSet)`
        *   Return `true` if `inputSet` matches the original set used in construction (ignoring order), `false` otherwise.

*   Handle empty `CircularList`:
    *   Iterating an empty list should produce no elements.
    *   `hasNext()` on an empty list iterator returns `false`.
    *   `next()` on an empty list iterator throws `java.util.NoSuchElementException`.

*   Update `KillUnusedSegments` coordinator duty:
    *   Use `CircularList` to select datasources in a round-robin manner, avoiding consecutive selection of the same datasource when others are available.
    *   Refresh the internal circular datasource list when the set of datasources changes between runs.
    *   Continue selecting a single eligible datasource in each run if no others are available.
    *   When a datasource initially has no unused segments, ensure `Stats.Kill.ELIGIBLE_UNUSED_SEGMENTS` and `Stats.Kill.SUBMITTED_TASKS` are `0`. Correctly process it in subsequent runs when unused segments appear.

*   Accumulate statistics:
    *   `Stats.Kill.AVAILABLE_SLOTS` should reflect the cumulative sum of available slots across all runs.
    *   `Stats.Kill.SUBMITTED_TASKS` should reflect the cumulative sum of submitted tasks across all runs.
    *   `Stats.Kill.MAX_SLOTS` should reflect the maximum slots across all runs.
    *   `Stats.Kill.ELIGIBLE_UNUSED_SEGMENTS` should reflect the count of eligible unused segments per datasource per run.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.