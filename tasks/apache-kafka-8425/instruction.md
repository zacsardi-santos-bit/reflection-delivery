Implement a feature in Kafka Streams to optimize task rebalancing by identifying clients that are fully caught up with stateful tasks and allowing immediate task reassignment without unnecessary warmup phases. Update the assignment logic to incorporate this optimization.

Requirements:

* Implement a static method `tasksToCaughtUpClients` in `RankedClient`:
    * Location: `streams/src/main/java/org/apache/kafka/streams/processor/internals/assignment/RankedClient.java`
    * Signature: `static Map<TaskId, SortedSet<UUID>> tasksToCaughtUpClients(SortedMap<TaskId, SortedSet<RankedClient>> statefulTasksToRankedClients)`
    * Functionality: Return a map of tasks to UUIDs of clients that are fully caught up (rank is `Task.LATEST_OFFSET` or `0L`). Exclude tasks with no caught-up clients. Return an empty map if no tasks have caught-up clients.

* Update `assign` method in `DefaultStateConstrainedBalancedAssignor`:
    * Location: `streams/src/main/java/org/apache/kafka/streams/processor/internals/assignment/DefaultStateConstrainedBalancedAssignor.java`
    * Signature: `Map<UUID, List<TaskId>> assign(SortedMap<TaskId, SortedSet<RankedClient>> statefulTasksToRankedClients, int balanceFactor, Set<UUID> clients, Map<UUID, Integer> clientsToNumberOfStreamThreads, Map<TaskId, SortedSet<UUID>> tasksToCaughtUpClients)`
    * Add a fifth parameter `tasksToCaughtUpClients` to represent fully caught-up clients for each task.
    * Update all internal helper methods to accept `Map<TaskId, SortedSet<UUID>>` for `tasksToCaughtUpClients`.
    * Remove the private `tasksToCaughtUpClients` helper method from this class.

* Update `getMovements` method in `TaskMovement`:
    * Location: `streams/src/main/java/org/apache/kafka/streams/processor/internals/assignment/TaskMovement.java`
    * Signature: `static List<TaskMovement> getMovements(Map<UUID, List<TaskId>> statefulActiveTaskAssignment, Map<UUID, List<TaskId>> balancedStatefulActiveTaskAssignment, Map<TaskId, SortedSet<UUID>> tasksToCaughtUpClients, int maxWarmupReplicas)`
    * Add a third parameter `tasksToCaughtUpClients`.
    * Perform immediate task movement if the destination client is in the caught-up set for that task, without adding to the movements list.
    * Schedule tasks as warmup movements if the destination client is not caught up, respecting `maxWarmupReplicas`.
    * Throw `IllegalStateException` if a task in `statefulActiveTaskAssignment` lacks a destination in `balancedStatefulActiveTaskAssignment`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.