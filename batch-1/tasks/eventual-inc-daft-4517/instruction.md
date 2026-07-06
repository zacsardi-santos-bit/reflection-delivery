Implement descriptive naming and visualization for distributed pipeline nodes in Daft's distributed execution engine. Update node names to include a "Distributed" prefix and ensure visualization support for both ASCII and Mermaid formats. Modify related internal methods to handle collections of inputs and integrate execution configuration into the plan structure.

Requirements:

*   Update node names:
    *   InMemorySourceNode must return 'DistributedInMemoryScan' from its name() method.
    *   ScanSourceNode must return 'DistributedScan' from its name() method.
    *   LimitNode must return 'DistributedLimit' from its name() method.
    *   IntermediateNode must return 'DistributedIntermediateNode' from its name() method.

*   Implement visualization support:
    *   Add an as_tree_display() method to the DistributedPipelineNode trait, returning a &dyn TreeDisplay reference.
    *   Ensure all concrete node types (InMemorySourceNode, ScanSourceNode, LimitNode, IntermediateNode, ActorUDF) implement the as_tree_display() method.
    *   Implement the TreeDisplay trait for all concrete pipeline node types, providing display_as(), get_children(), and get_name() methods.
    *   Provide two public visualization functions:
        *   viz_distributed_pipeline_ascii(root: &dyn DistributedPipelineNode, simple: bool) -> String
        *   viz_distributed_pipeline_mermaid(root: &dyn DistributedPipelineNode, display_type: DisplayLevel, bottom_up: bool, subgraph_options: Option<SubgraphOptions>) -> String

*   Modify internal methods:
    *   Rename make_task_for_partition_ref to make_task_for_partition_refs in InMemorySourceNode, accepting Vec<PartitionRef>.
    *   Update make_source_tasks in ScanSourceNode to accept Arc<Vec<ScanTaskLikeRef>>.

*   Integrate execution configuration:
    *   Store and expose DaftExecutionConfig in StagePlan via execution_config() method.
    *   Ensure StagePlan::from_logical_plan accepts an Arc<DaftExecutionConfig> parameter.

*   Expose visualization methods in DistributedPhysicalPlan:
    *   Implement repr_ascii(simple: bool) and repr_mermaid(options) methods callable from Python.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.