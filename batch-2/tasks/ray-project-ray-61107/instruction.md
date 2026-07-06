I'm working on the operator graph used in the streaming execution layer of a data processing framework.

*   LogicalOperator instances (and instances of the base Operator class) must NOT initialize or maintain an '_output_dependencies' attribute at any point during construction or after calling '_apply_transform'.

*   LogicalOperator._apply_transform called with an identity transform must return the original operator unchanged (same object), and the operator and its inputs must still have no '_output_dependencies' attribute after the call.

*   PhysicalOperator must maintain an 'output_dependencies' property returning the list of downstream PhysicalOperators that consume this operator's output; when a downstream operator is constructed with a given operator as input, that input's 'output_dependencies' must include the downstream operator.

*   PhysicalOperator._apply_transform must return a new node (not the original) when any input operator is replaced by the transform, and the new node must have a distinct 'id' and distinct 'metrics' object compared to the original node.

*   After PhysicalOperator._apply_transform replaces an input, the returned node's 'input_dependencies' must reflect the replacements, the returned node must appear in the 'output_dependencies' of every one of its inputs (both replaced and unchanged), and the old root node must NOT appear in any input's 'output_dependencies'.

*   When the transform function itself replaces the current node being processed, PhysicalOperator._apply_transform must register the replacement node in the correct inputs' 'output_dependencies' and must remove the old node from all inputs' 'output_dependencies'.

*   In a deep operator chain where multiple nodes are replaced, PhysicalOperator._apply_transform must leave no stale downstream references: each intermediate node's 'output_dependencies' must contain only the correctly transformed downstream node and must not reference any old replaced nodes.

*   If a transform function mutates an operator's '_input_dependencies' in-place and returns the same operator object, PhysicalOperator._apply_transform must raise an AssertionError with the message 'In-place input mutation is not supported; return a new node instead.'


*   Interface details: Type: Class
Name: Operator
Location: python/ray/data/_internal/logical/interfaces/operator.py
Description: Base class for all operators. Must NOT initialize or maintain '_output_dependencies' attribute. Must NOT define a 'output_dependencies' property. The '_apply_transform' method must not create '_output_dependencies' on any operator instance.
Signature: _apply_transform(transform: Callable[["Operator"], "Operator"]) -> "Operator"

Type: Class
Name: LogicalOperator
Location: python/ray/data/_internal/logical/interfaces/operator.py
Description: Logical operator extending Operator. Must not have '_output_dependencies' attribute on any instance, before or after any '_apply_transform' call.

Type: Class
Name: PhysicalOperator
Location: python/ray/data/_internal/execution/interfaces/physical_operator.py
Description: Physical operator that maintains reverse-dependency tracking via 'output_dependencies'. Must implement '_apply_transform' that correctly rewires 'output_dependencies' on all affected operators after any transformation.

Signature: output_dependencies -> List["PhysicalOperator"]
  Returns the list of downstream PhysicalOperators that consume this operator's output. Updated automatically when downstream operators are constructed and during transforms.

Signature: _apply_transform(transform: Callable[["PhysicalOperator"], "PhysicalOperator"]) -> "PhysicalOperator"
  Recursively applies transform to the operator graph. When any node is replaced, returns a new root node with a distinct 'id' and distinct 'metrics', correctly wired 'input_dependencies', and updated 'output_dependencies' on all inputs. Old nodes are removed from their inputs' 'output_dependencies'. If the transform mutates '_input_dependencies' in-place and returns the same node, raises AssertionError with the message: "In-place input mutation is not supported; return a new node instead."

Attributes required on PhysicalOperator after transform:
  - id: unique string identifier (new node must have a different id than the original)
  - metrics: OpRuntimeMetrics instance (new node must have a distinct metrics object)
  - input_dependencies: List["PhysicalOperator"]
  - output_dependencies: List["PhysicalOperator"]


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.