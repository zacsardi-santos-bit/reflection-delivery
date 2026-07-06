I'm working on simplifying the Kafka Streams state management API.

*   The StateManager interface must expose a single commit() method that replaces the previously separate flush() and checkpoint() methods. The flush() and checkpoint() methods must be removed from the interface.

*   The maybeCheckpoint() method on the Task interface must take no parameters. The previous boolean enforceCheckpoint parameter must be removed entirely.

*   ProcessorStateManager.commit() must flush all registered state stores and write the offset checkpoint file. It must throw ProcessorStateException when a state store throws an exception, and preserve StreamsException when a state store propagates one.

*   GlobalStateManagerImpl.commit() must commit all registered state stores and write the offset checkpoint file. It must throw StreamsException if any store commit fails or if writing the checkpoint file fails (such as when the file is not writable).

*   StandbyTask.maybeCheckpoint() (no parameters) must call stateManager.commit() unconditionally. On close dirty, stateManager.commit() must NOT be called. On close clean or suspend-then-commit, stateManager.commit() must be called. A RuntimeException thrown by stateManager.commit() during close clean must propagate.

*   StreamTask.maybeCheckpoint() (no parameters) must call stateManager.commit(). After task restoration with at-least-once processing, stateManager.commit() must be called exactly once. After task restoration with exactly-once processing, stateManager.commit() must NOT be called. postCommit() must call stateManager.commit() for suspended tasks. On close dirty, stateManager.commit() must NOT be called. On close clean, stateManager.commit() must be called.

*   The DefaultStateUpdater must call task.maybeCheckpoint() (no arguments) on tasks when performing periodic checkpoints or during task removal and task pause operations.

*   StateManagerStub (test class at streams/src/test/java/.../StateManagerStub.java) must implement commit() as a no-op, replacing the previous flush() implementation. The checkpoint() method must be removed.

*   GlobalStateManagerStub (test class at streams/src/test/java/org/apache/kafka/test/GlobalStateManagerStub.java) must have a single public boolean field named committed replacing the previous flushed and checkpointWritten fields. It must implement commit() which sets committed = true. The flush() and checkpoint() methods must be removed.


*   Interface details: ## Interfaces Required by Tests

### StateManager interface
Type: Interface Method
Name: commit
Location: streams/src/main/java/org/apache/kafka/streams/processor/internals/StateManager.java
Signature: void commit()
Description: Replaces the previously separate flush() and checkpoint() methods. A single operation that both flushes registered state stores and writes offset checkpoints. The flush() and checkpoint() methods must be removed from this interface.

---

### Task interface
Type: Interface Method
Name: maybeCheckpoint
Location: streams/src/main/java/org/apache/kafka/streams/processor/internals/Task.java
Signature: void maybeCheckpoint()
Description: Takes no parameters. Previously accepted a boolean enforceCheckpoint argument. The parameter is removed entirely; all invocations call this method with no arguments.

---

### StateManagerStub (test infrastructure)
Type: Class
Name: StateManagerStub
Location: streams/src/test/java/org/apache/kafka/streams/processor/internals/StateManagerStub.java
Description: Test stub implementing StateManager. Must implement commit() as a no-op method, replacing the previous flush() no-op. The checkpoint() method must be removed entirely.
Signature: public void commit()

---

### GlobalStateManagerStub (test infrastructure)
Type: Class
Name: GlobalStateManagerStub
Location: streams/src/test/java/org/apache/kafka/test/GlobalStateManagerStub.java
Description: Test stub implementing GlobalStateManager. Must have a single public boolean field named committed (replacing the previous separate flushed and checkpointWritten fields). Must implement commit() that sets committed = true. The flush() and checkpoint() methods must be removed.
Fields: public boolean committed
Signature: public void commit() { committed = true; }

---

### ProcessorStateManager
Type: Class Method
Name: commit
Location: streams/src/main/java/org/apache/kafka/streams/processor/internals/ProcessorStateManager.java
Signature: void commit()
Description: Replaces flush(). Commits all registered state stores and writes offset checkpoints. Throws ProcessorStateException when a state store throws an exception during commit. Throws StreamsException when a StreamsException is propagated from a state store.

---

### GlobalStateManagerImpl
Type: Class Method
Name: commit
Location: streams/src/main/java/org/apache/kafka/streams/processor/internals/GlobalStateManagerImpl.java
Signature: void commit()
Description: Replaces flush(). Commits all registered state stores and writes the offset checkpoint file. Throws StreamsException if any registered store fails to commit. Also throws StreamsException if writing the checkpoint file fails.

---

### StandbyTask
Type: Class Method
Name: maybeCheckpoint
Location: streams/src/main/java/org/apache/kafka/streams/processor/internals/StandbyTask.java
Signature: void maybeCheckpoint()
Description: Takes no parameters (boolean parameter removed). When called, unconditionally invokes stateManager.commit(). On close dirty, stateManager.commit() must NOT be called. On close clean or suspend+commit, stateManager.commit() IS called. If stateManager.commit() throws a RuntimeException during close clean, the exception propagates.

---

### StreamTask
Type: Class Method
Name: maybeCheckpoint
Location: streams/src/main/java/org/apache/kafka/streams/processor/internals/StreamTask.java
Signature: void maybeCheckpoint()
Description: Takes no parameters (boolean parameter removed). Invokes stateManager.commit() when called. After restoration with at-least-once processing, stateManager.commit() is called. After restoration with exactly-once processing, stateManager.commit() is NOT called. postCommit() calls stateManager.commit() for suspended tasks. On close dirty, stateManager.commit() is NOT called. On close clean, stateManager.commit() IS called.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.