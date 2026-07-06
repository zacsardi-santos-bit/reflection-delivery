I'm working on the snapshot system in rqlite and I'd like to improve the API for determining what kind of snapshot to take next.

*   The snapshot package must export a type named Type (an integer) to represent the kind of snapshot, replacing the existing SnapshotType.

*   The snapshot package must export a constant named Full of type Type as the first iota value, replacing SnapshotTypeFull.

*   The snapshot package must export a constant named Incremental of type Type as the second iota value, replacing SnapshotTypeIncremental.

*   The Snapshot struct's typ field must use type Type (not SnapshotType), so that Full and Incremental are directly assignable to it.

*   The snapshot Store must expose a method DueNext() (Type, error) that returns Full when the store contains no snapshots or when a full-needed flag file exists, and returns Incremental otherwise.

*   The snapshot Store must expose a method SetDueNext(t Type) error that sets the state: setting Full creates the full-needed flag file; setting Incremental removes it.

*   The SnapshotStore interface in the store package must declare DueNext() (snapshot.Type, error) replacing FullNeeded() (bool, error).

*   The SnapshotStore interface in the store package must declare SetDueNext(snapshot.Type) error replacing SetFullNeeded() error.

*   After a successful snapshot is taken, DueNext() must return Incremental.

*   After loading a new database into the store (e.g., via a load command), DueNext() must return Full.

*   When no snapshots have ever been successfully committed to the store (store is empty), DueNext() must return Full.

*   After a vacuum operation that does not require a new full snapshot, DueNext() must return Incremental.


*   Interface details: Type: TypeDefinition
Name: Type
Location: snapshot/snapshot.go
Description: Integer type representing the kind of snapshot. Replaces the previous SnapshotType. Used as the return type of DueNext() and the parameter type of SetDueNext().

Type: Constant
Name: Full
Location: snapshot/snapshot.go
Description: Constant of type Type (value iota = 0) indicating a full snapshot (containing a database file). Replaces SnapshotTypeFull.

Type: Constant
Name: Incremental
Location: snapshot/snapshot.go
Description: Constant of type Type indicating an incremental snapshot (containing only WAL files). Replaces SnapshotTypeIncremental.

Type: Method
Name: DueNext
Location: snapshot/store.go
Signature: DueNext() (Type, error)
Description: Returns the type of snapshot due next. Returns Full when the store has no snapshots or when a full-needed flag is set; returns Incremental otherwise. Replaces the previous FullNeeded() (bool, error) method.

Type: Method
Name: SetDueNext
Location: snapshot/store.go
Signature: SetDueNext(t Type) error
Description: Sets the type of snapshot due next. Setting Full creates a flag file; setting Incremental removes it. Replaces the previous SetFullNeeded() error method.

Type: InterfaceMethod
Name: DueNext
Location: store/store.go (SnapshotStore interface)
Signature: DueNext() (snapshot.Type, error)
Description: Interface method on SnapshotStore replacing FullNeeded() (bool, error). Must be satisfied by any snapshot store implementation used by the store package.

Type: InterfaceMethod
Name: SetDueNext
Location: store/store.go (SnapshotStore interface)
Signature: SetDueNext(snapshot.Type) error
Description: Interface method on SnapshotStore replacing SetFullNeeded() error. Must be satisfied by any snapshot store implementation used by the store package.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.