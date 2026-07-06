I'm working on adding lifecycle hook support to the Pulumi Configuration Language binder.

*   The PCL binder must support a top-level 'hook' block with exactly one label (the hook name). The binder must declare Hook nodes into the program's node graph so they appear in program.Nodes and are returned by program.Hooks().

*   Hook blocks must accept exactly two attributes: 'command' and 'onDryRun'. Any other attribute must produce a diagnostic error (severity: error) with the summary: "unknown property '<name>' among [command onDryRun]" (with the property list sorted as [command onDryRun]).

*   When binding the 'command' attribute of a hook block, a special variable 'args' must be introduced in scope. The 'args' variable must have string-typed fields 'urn', 'id', 'name', 'type', and dynamic-map-typed fields 'new_inputs', 'old_inputs', 'new_outputs', 'old_outputs'. References to these fields must resolve without errors.

*   The 'args' variable must NOT be in scope when binding the 'onDryRun' attribute. Referencing 'args' inside 'onDryRun' must produce a diagnostic error (severity: error) with the summary: "undefined variable args".

*   The 'command' attribute of a hook block must be type-checked against a list-of-strings type. Primitive values such as booleans and integers are safely convertible to string and must not produce errors. Complex types such as empty tuples and empty objects are not convertible and must produce a diagnostic error (severity: error) with the summary: "cannot assign expression of type ((), {}) to location of type list(output(string) | string) | output(list(string)): ".

*   The Program type must expose a Hooks() method that returns []*Hook containing all Hook nodes present in program.Nodes.

*   The ResourceOptions struct must have a Hooks field of type model.Expression. When a resource's options block contains a 'hooks' attribute, the Hooks field must be set to that expression. Calling Evaluate(&hcl.EvalContext{}) on the expression must return a cty object where keys are lifecycle event names and values are cty tuples of hook name strings. For example, 'hooks = { beforeCreate = [foo] }' must evaluate to cty.ObjectVal({"beforeCreate": cty.TupleVal([cty.StringVal("foo")])}).

*   The valid hook lifecycle event names accepted in a resource's hooks option are: 'beforeCreate', 'afterCreate', 'beforeUpdate', 'afterUpdate', 'beforeDelete', 'afterDelete'. Using any other name must produce a diagnostic error (severity: error) with the summary: "unknown hook name '<name>'" where <name> is the unrecognized name.

*   The 'hooks' attribute in resource options must be an object expression. If a non-object value is provided (e.g. a list), a diagnostic error (severity: error) must be produced with the summary: "hooks option must be an object mapping hook names to lists of hook references". Each value in the hooks object must be a list expression (tuple); if a bare non-list value is provided for any key, the same error summary must be produced.


*   Interface details: Type: Struct
Name: Hook
Location: pkg/codegen/pcl/hook.go
Description: Represents a named resource lifecycle hook block in a PCL program. Implements the Node interface and is stored in program.Nodes alongside resources and other nodes. Supports type assertion as *pcl.Hook when iterating over program.Nodes.

Type: Method
Name: Hooks
Location: pkg/codegen/pcl/program.go
Signature: (p *Program) Hooks() []*Hook
Description: Returns all Hook nodes from the program's Nodes list. Returns a []*Hook slice (may be nil if there are no hooks).

Type: Field
Name: Hooks
Location: pkg/codegen/pcl/resource.go — inside the ResourceOptions struct
Type: model.Expression
Description: Holds the hooks object expression parsed from a resource's options block. When evaluated with hookBindings.Evaluate(&hcl.EvalContext{}), it returns a cty object where each key is a valid lifecycle event name (e.g. "beforeCreate") and each value is a cty tuple of hook name strings (e.g. cty.TupleVal([]cty.Value{cty.StringVal("hookName")})).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.