Implement a dependency management component for events that tracks relationships in a tree structure. Ensure that adding or removing events automatically manages their dependencies, including shared dependencies. Provide observer support for event addition and removal notifications.

*   Implement `NewDependenciesManager` in `pkg/events/dependencies/manager.go`:
    *   Accept a function mapping an event ID to its dependencies.
    *   Return a `*Manager` using this function to look up dependencies.

*   Implement `Manager` class in `pkg/events/dependencies/manager.go`:
    *   `SubscribeAdd(onAdd func(*EventNode))`: Register callbacks for event node additions.
    *   `SubscribeRemove(onRemove func(*EventNode))`: Register callbacks for event node removals.
    *   `SelectEvent(id events.ID) *EventNode`: Add an event and its transitive dependencies to the tree.
        *   Ensure `GetEvent(id)` returns the corresponding `*EventNode` and true.
        *   Include the event ID in each dependency node's `GetDependants()`.
    *   `UnselectEvent(id events.ID) bool`: Soft-remove an event if no other event depends on it.
        *   Remove orphaned dependencies.
        *   Return false if the event is still needed by another event.
    *   `RemoveEvent(id events.ID)`: Remove an event and its dependencies if unreferenced.
        *   Ensure `GetEvent(id)` returns (nil, false) after removal.
        *   Cascade removal to dependent events.
    *   `GetEvent(id events.ID) (*EventNode, bool)`: Retrieve an event node and its existence status.

*   Implement `EventNode` class in `pkg/events/dependencies/event.go`:
    *   `GetID() events.ID`: Return the event's ID.
    *   `GetDependencies() events.Dependencies`: Return the event's dependencies.
    *   `GetDependants() []events.ID`: Return a list of event IDs that depend on this node.

*   Ensure observer callbacks:
    *   Invoke `SubscribeAdd` callbacks for each added node, including transitive dependencies.
    *   Invoke `SubscribeRemove` callbacks for each removed node, including cascading removals.

*   Maintain shared dependencies:
    *   Do not remove shared dependencies if still referenced by other events.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.