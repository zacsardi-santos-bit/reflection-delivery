I'm working on improving how the server handles CHASM side-effect tasks on standby clusters in a multi-datacenter setup.

*   A new optional interface must be defined that side-effect task executors can implement to declare custom discard behavior for standby clusters. This interface has a single method that receives a context, a component reference, task attributes, and the typed task data, and returns an error.

*   The NewRegistrableSideEffectTask function must automatically detect whether the provided executor implements the discard interface (using a type assertion) and, if so, wire up a discard function on the resulting RegistrableTask. If the executor does not implement the discard interface, no discard function is set.

*   RegistrableTask must expose a HasDiscardHandler() bool method that returns true if a discard function was registered (i.e., the executor implements the discard interface), and false otherwise. Pure tasks must always return false from HasDiscardHandler().

*   Node.ExecuteSideEffectTask must be updated to remove the *Registry parameter from its signature. The node must use its own internal registry field to look up the task type.

*   A new Node.ExecuteSideEffectDiscardTask method must be added with the same signature shape as ExecuteSideEffectTask (context, execution key, task, validation function). It must invoke the registered discard function instead of the execute function. When the task validator returns (false, nil), ExecuteSideEffectDiscardTask must return a *serviceerror.NotFound error. When the validator returns an error, that error must be returned. When the discard function returns an error, that error must be returned.

*   The ChasmTree interface must be updated: ExecuteSideEffectTask must drop the *chasm.Registry parameter, and a new ExecuteSideEffectDiscardTask method with the signature (ctx context.Context, executionKey chasm.ExecutionKey, task *tasks.ChasmTask, validate func(chasm.NodeBackend, chasm.Context, chasm.Component) error) error must be added.

*   The MockChasmTree in service/history/interfaces/chasm_tree_mock.go must be regenerated to match the updated ChasmTree interface: ExecuteSideEffectTask must accept 4 arguments (no registry), and a new ExecuteSideEffectDiscardTask mock method with 4 arguments must be added.

*   newOutboundQueueStandbyTaskExecutor must accept a new clientBean client.Bean parameter (as the last argument after chasmEngine) and store it on the executor struct for use during the discard path.

*   executeChasmSideEffectTask in service/history/chasm_task_util.go must remove its registry *chasm.Registry parameter. All callers must be updated accordingly.

*   A new discardChasmSideEffectTask function must be added to service/history/chasm_task_util.go. It must: (1) check whether the execution still exists on the source cluster and return nil if it does not; (2) look up the task type in the registry and return an internal error if not found; (3) if HasDiscardHandler() is false, log a warning and return consts.ErrTaskDiscarded; (4) if HasDiscardHandler() is true, call tree.ExecuteSideEffectDiscardTask and return its result.

*   Standby task executors (outbound queue, timer queue, and transfer queue) must, when the CHASM standby discard delay has elapsed for a side-effect task, call discardChasmSideEffectTask instead of the previous behavior. If the task has a discard handler the execution proceeds via ExecuteSideEffectDiscardTask; if not, consts.ErrTaskDiscarded is returned. The ExecutedAsActive field of the execute response must be false in both cases.

*   The noopChasmTree implementation in service/history/workflow/noop_chasm_tree.go must implement the updated ChasmTree interface, providing no-op implementations of both the updated ExecuteSideEffectTask and the new ExecuteSideEffectDiscardTask.


*   Interface details: Type: Interface
Name: SideEffectTaskDiscarder
Location: chasm/task.go
Signature: Discard(ctx context.Context, ref ComponentRef, attrs TaskAttributes, task T) error
Description: An optional interface that a side-effect task executor may implement to define custom discard behavior on standby clusters. When a side-effect task has been pending on standby past the configured discard delay, the framework calls Discard instead of silently dropping the task. Implementations must not mutate component state on standby clusters. The context always carries engine access.

Type: Method
Name: HasDiscardHandler
Location: chasm/registrable_task.go
Signature: (rt *RegistrableTask) HasDiscardHandler() bool
Description: Returns true if the task's executor implements SideEffectTaskDiscarder (i.e., the task has a custom discard handler registered). Returns false for pure tasks and for side-effect tasks whose executor does not implement SideEffectTaskDiscarder.

Type: Method
Name: ExecuteSideEffectTask
Location: chasm/tree.go
Signature: (n *Node) ExecuteSideEffectTask(ctx context.Context, executionKey ExecutionKey, chasmTask *tasks.ChasmTask, validate func(NodeBackend, Context, Component) error) error
Description: Executes the side-effect task on the node. The registry parameter that previously appeared in this signature has been removed; the node now uses its own internal registry. Behavior on validation failure returns *serviceerror.NotFound. Returns the validation or execution error as appropriate.

Type: Method
Name: ExecuteSideEffectDiscardTask
Location: chasm/tree.go
Signature: (n *Node) ExecuteSideEffectDiscardTask(ctx context.Context, executionKey ExecutionKey, chasmTask *tasks.ChasmTask, validate func(NodeBackend, Context, Component) error) error
Description: Executes the discard handler for the given ChasmTask. Called on standby clusters when a side-effect task has been pending past the discard delay. When the validator returns (false, nil), returns a *serviceerror.NotFound error. When the validator returns an error, returns that error. When the discard handler returns an error, returns that error. Panics (via internal assertion) if called on a task whose executor does not implement SideEffectTaskDiscarder.

Type: Interface Method
Name: ExecuteSideEffectDiscardTask
Location: service/history/interfaces/chasm_tree.go
Signature: ExecuteSideEffectDiscardTask(ctx context.Context, executionKey chasm.ExecutionKey, task *tasks.ChasmTask, validate func(chasm.NodeBackend, chasm.Context, chasm.Component) error) error
Description: New method added to the ChasmTree interface. Must be implemented by all ChasmTree implementations, including the noop tree. Delegates to Node.ExecuteSideEffectDiscardTask.

Type: Interface Method (updated)
Name: ExecuteSideEffectTask
Location: service/history/interfaces/chasm_tree.go
Signature: ExecuteSideEffectTask(ctx context.Context, executionKey chasm.ExecutionKey, task *tasks.ChasmTask, validate func(chasm.NodeBackend, chasm.Context, chasm.Component) error) error
Description: The previously existing ExecuteSideEffectTask method on the ChasmTree interface. The *chasm.Registry parameter that was previously the second argument has been removed.

Type: Mock Method (updated)
Name: ExecuteSideEffectTask
Location: service/history/interfaces/chasm_tree_mock.go
Signature: (m *MockChasmTree) ExecuteSideEffectTask(ctx context.Context, executionKey chasm.ExecutionKey, task *tasks.ChasmTask, validate func(chasm.NodeBackend, chasm.Context, chasm.Component) error) error
Description: Updated mock for ExecuteSideEffectTask. The *chasm.Registry parameter has been removed. The mock recorder's method signature must match: ExecuteSideEffectTask(ctx, executionKey, task, validate any).

Type: Mock Method (new)
Name: ExecuteSideEffectDiscardTask
Location: service/history/interfaces/chasm_tree_mock.go
Signature: (m *MockChasmTree) ExecuteSideEffectDiscardTask(ctx context.Context, executionKey chasm.ExecutionKey, task *tasks.ChasmTask, validate func(chasm.NodeBackend, chasm.Context, chasm.Component) error) error
Description: New mock method for ExecuteSideEffectDiscardTask on MockChasmTree. The mock recorder's method signature: ExecuteSideEffectDiscardTask(ctx, executionKey, task, validate any).

Type: Function (updated)
Name: newOutboundQueueStandbyTaskExecutor
Location: service/history/outbound_queue_standby_task_executor.go
Signature: newOutboundQueueStandbyTaskExecutor(shardCtx historyi.ShardContext, workflowCache wcache.Cache, clusterName string, logger log.Logger, metricsHandler metrics.Handler, chasmEngine chasm.Engine, clientBean client.Bean) *outboundQueueStandbyTaskExecutor
Description: Constructor for the standby outbound queue task executor. A new clientBean client.Bean parameter has been added at the end. This is required to support the discard path that checks whether an execution still exists on the source cluster.

Type: Function (updated)
Name: executeChasmSideEffectTask
Location: service/history/chasm_task_util.go
Signature: executeChasmSideEffectTask(ctx context.Context, engine chasm.Engine, tree historyi.ChasmTree, task *tasks.ChasmTask) error
Description: Executes a CHASM side-effect task after physical task validation. The registry *chasm.Registry parameter that was previously the second argument has been removed.

Type: Function (new)
Name: discardChasmSideEffectTask
Location: service/history/chasm_task_util.go
Signature: discardChasmSideEffectTask(ctx context.Context, engine chasm.Engine, registry *chasm.Registry, tree historyi.ChasmTree, task *tasks.ChasmTask, logger log.Logger, clusterName string, clientBean client.Bean, namespaceRegistry namespace.Registry) error
Description: Handles discard of a CHASM side-effect task on standby clusters. First checks if the execution still exists on the source (active) cluster — if gone, returns nil. If the execution exists and the task's executor implements SideEffectTaskDiscarder (HasDiscardHandler() == true), calls tree.ExecuteSideEffectDiscardTask. If the executor does not implement SideEffectTaskDiscarder, logs a warning and returns consts.ErrTaskDiscarded.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.