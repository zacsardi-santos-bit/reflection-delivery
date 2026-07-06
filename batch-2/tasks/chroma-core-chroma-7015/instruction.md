I'm working on a system where async functions are attached to data collections and run asynchronously as data is ingested.

*   TryFinishAsyncAttachedFunctionInvocation must execute its logic inside a database transaction. Within the transaction, it must fetch the attached function by its ID (with a row lock), retrieve the associated function record, retrieve the collection record, and then call UpdateCompletionOffsetAndHeapEntry with the attached function's UUID, the collection ID string, and the new completion offset cast to int64.

*   When UpdateCompletionOffsetAndHeapEntry returns common.ErrAttachedFunctionOffsetWouldRegress, TryFinishAsyncAttachedFunctionInvocation must propagate the error (return a non-nil error).

*   When the collection's current LogPosition (int64) is strictly greater than the new completion offset (cast to int64), TryFinishAsyncAttachedFunctionInvocation must return a response whose Result is a NeedsRepair value with CurrentCollectionLogOffset set to the current collection log position (as uint64). No error is returned in this case.

*   When the collection's current LogPosition (int64) is less than or equal to the new completion offset (cast to int64) and UpdateCompletionOffsetAndHeapEntry returns nil, TryFinishAsyncAttachedFunctionInvocation must return a response whose Result is a Success value with UpdatedCompletionOffset set to the new completion offset (uint64). No error is returned.

*   TryFinishAsyncAttachedFunctionInvocation must be idempotent with respect to its response: calling it multiple times with the same request returns the same response type (NeedsRepair or Success) each time. UpdateCompletionOffsetAndHeapEntry is called on every invocation.

*   FinalizeAsyncAttachedFunctionRepair must call UpdateHeapEntryPending with the attached function's UUID (parsed from the request's AttachedFunctionId string) and pending=false. It must return a non-nil response and nil error on success.

*   FinalizeAsyncAttachedFunctionRepair must be idempotent: calling it multiple times with the same AttachedFunctionId returns success each time, and UpdateHeapEntryPending is called on every invocation.

*   UpdateCompletionOffsetAndHeapEntry must be added to the IAttachedFunctionDb interface in go/pkg/sysdb/metastore/db/dbmodel/ with signature: UpdateCompletionOffsetAndHeapEntry(id uuid.UUID, collectionID string, newOffset int64) error. It must return common.ErrAttachedFunctionOffsetWouldRegress when the update would cause the offset to regress (no rows affected due to a WHERE guard clause).

*   UpdateHeapEntryPending must be added to the IAttachedFunctionDb interface in go/pkg/sysdb/metastore/db/dbmodel/ with signature: UpdateHeapEntryPending(id uuid.UUID, pending bool) error.

*   A sentinel error common.ErrAttachedFunctionOffsetWouldRegress must be defined in the go/pkg/common/ package. This error is returned by UpdateCompletionOffsetAndHeapEntry when no rows are updated because the new offset is not greater than the current stored offset.

*   Proto messages TryFinishAsyncAttachedFunctionInvocationRequest (fields: AttachedFunctionId string, CollectionId string, NewCompletionOffset uint64), TryFinishAsyncAttachedFunctionInvocationResponse (oneof Result with NeedsRepair containing CurrentCollectionLogOffset uint64 and Success containing UpdatedCompletionOffset uint64), FinalizeAsyncAttachedFunctionRepairRequest (field: AttachedFunctionId string), and FinalizeAsyncAttachedFunctionRepairResponse must be defined in the coordinatorpb proto and generated into go/pkg/proto/coordinatorpb/.

*   The mock for IAttachedFunctionDb (at go/pkg/sysdb/metastore/db/dbmodel/mocks/) must include implementations of both UpdateCompletionOffsetAndHeapEntry and UpdateHeapEntryPending to satisfy the updated interface.


*   Interface details: Type: Method
Name: TryFinishAsyncAttachedFunctionInvocation
Location: go/pkg/sysdb/coordinator/coordinator.go (or a new file in go/pkg/sysdb/coordinator/)
Signature: TryFinishAsyncAttachedFunctionInvocation(ctx context.Context, req *coordinatorpb.TryFinishAsyncAttachedFunctionInvocationRequest) (*coordinatorpb.TryFinishAsyncAttachedFunctionInvocationResponse, error)
Receiver: *Coordinator (defined in package coordinator at go/pkg/sysdb/coordinator/)
Description: Attempts to finalize an async attached function's invocation by updating its completion offset atomically. Executes inside a database transaction: fetches the attached function by ID (with row lock), fetches the associated function and collection, then calls UpdateCompletionOffsetAndHeapEntry. Returns a NeedsRepair result (with CurrentCollectionLogOffset) if the collection log position exceeds the new offset, a Success result (with UpdatedCompletionOffset) if not, or an error if the offset would regress.

Type: Method
Name: FinalizeAsyncAttachedFunctionRepair
Location: go/pkg/sysdb/coordinator/coordinator.go (or a new file in go/pkg/sysdb/coordinator/)
Signature: FinalizeAsyncAttachedFunctionRepair(ctx context.Context, req *coordinatorpb.FinalizeAsyncAttachedFunctionRepairRequest) (*coordinatorpb.FinalizeAsyncAttachedFunctionRepairResponse, error)
Receiver: *Coordinator (defined in package coordinator at go/pkg/sysdb/coordinator/)
Description: Finalizes a repair by calling UpdateHeapEntryPending with pending=false for the specified attached function ID. Idempotent — returns non-nil response and nil error on each successful call.

Type: Interface Method
Name: UpdateCompletionOffsetAndHeapEntry
Location: go/pkg/sysdb/metastore/db/dbmodel/ (IAttachedFunctionDb interface)
Signature: UpdateCompletionOffsetAndHeapEntry(id uuid.UUID, collectionID string, newOffset int64) error
Description: Atomically updates the completion offset and heap entry status for an attached function. Must return common.ErrAttachedFunctionOffsetWouldRegress (instead of nil) when the requested newOffset is less than or equal to the current stored completion offset (i.e., would cause the offset to regress). This new method must be added to the IAttachedFunctionDb interface and its mock (go/pkg/sysdb/metastore/db/dbmodel/mocks/IAttachedFunctionDb.go).

Type: Interface Method
Name: UpdateHeapEntryPending
Location: go/pkg/sysdb/metastore/db/dbmodel/ (IAttachedFunctionDb interface)
Signature: UpdateHeapEntryPending(id uuid.UUID, pending bool) error
Description: Updates the heap_entry_pending flag for an attached function. This new method must be added to the IAttachedFunctionDb interface and its mock (go/pkg/sysdb/metastore/db/dbmodel/mocks/IAttachedFunctionDb.go).

Type: Variable
Name: ErrAttachedFunctionOffsetWouldRegress
Location: go/pkg/common/ (errors.go or similar)
Signature: var ErrAttachedFunctionOffsetWouldRegress = errors.New("...") (or equivalent sentinel error)
Description: Sentinel error value returned by UpdateCompletionOffsetAndHeapEntry when the new offset would be lower than or equal to the current stored completion offset.

Type: Proto Message
Name: TryFinishAsyncAttachedFunctionInvocationRequest
Location: go/pkg/proto/coordinatorpb/ (generated from a .proto file)
Description: Request message for TryFinishAsyncAttachedFunctionInvocation. Fields: AttachedFunctionId string, CollectionId string, NewCompletionOffset uint64.

Type: Proto Message
Name: TryFinishAsyncAttachedFunctionInvocationResponse
Location: go/pkg/proto/coordinatorpb/ (generated from a .proto file)
Description: Response message with a oneof Result field. The Result can be one of:
  - NeedsRepair: a message containing CurrentCollectionLogOffset uint64 (accessed via TryFinishAsyncAttachedFunctionInvocationResponse_NeedsRepair wrapper type)
  - Success: a message containing UpdatedCompletionOffset uint64 (accessed via TryFinishAsyncAttachedFunctionInvocationResponse_Success wrapper type)

Type: Proto Message
Name: FinalizeAsyncAttachedFunctionRepairRequest
Location: go/pkg/proto/coordinatorpb/ (generated from a .proto file)
Description: Request message for FinalizeAsyncAttachedFunctionRepair. Fields: AttachedFunctionId string.

Type: Proto Message
Name: FinalizeAsyncAttachedFunctionRepairResponse
Location: go/pkg/proto/coordinatorpb/ (generated from a .proto file)
Description: Response message for FinalizeAsyncAttachedFunctionRepair. No fields required by tests.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.