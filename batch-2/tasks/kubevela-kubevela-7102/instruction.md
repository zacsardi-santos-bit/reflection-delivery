I'm working on a CUE code generator for component definitions that handles optional collection parameters like lists and maps.

*   Length conditions (LenEq, LenGt, LenGte, LenLt, LenLte) on StringParam, ArrayParam, and MapParam must generate CUE using bracket-access chained-guard syntax: `parameter["name"] != _|_ if len(parameter["name"]) <op> <n>` — NOT the old dot-syntax `len(parameter.name) <op> <n>`.

*   ArrayParam.Contains(val) must generate `parameter["name"] != _|_ if list.Contains(parameter["name"], val)` — NOT `list.Contains(parameter.name, val)`.

*   MapParam.HasKey(key) must generate `parameter["name"] != _|_ && parameter["name"].key != _|_` — NOT `parameter.name.key != _|_`.

*   ArrayParam.IsEmpty() and MapParam.IsEmpty() (and LenEq(0) on both) must return an AbsentOrEmptyCondition. At render time (SetIf, SpreadIf, validator FailWhen/OnlyWhen), this condition expands into TWO separate if blocks: one for `parameter["name"] == _|_` (absent branch) and one for `parameter["name"] != _|_ if len(parameter["name"]) == 0` (set-and-empty branch), each emitting the same body.

*   ArrayParam.IsNotEmpty() and MapParam.IsNotEmpty() must generate `parameter["name"] != _|_ if len(parameter["name"]) > 0`.

*   AllConditions(...) must join conditions with ` if ` when any sub-condition uses chained-guard syntax (LenCondition, ArrayContainsCondition); for non-chained conditions only, it joins with ` && `.

*   Logical OR (using Or()) must always join conditions with ` || ` regardless of whether any operand uses chained-guard syntax.

*   When an And/AllConditions compound includes a LenCondition or ArrayContainsCondition operand, the compound must NOT wrap the chained-guard operand in `(...) && (...)` form.

*   When a OneOf parameter has both Optional() and a Default value set, the generated parameter schema must render the discriminator field as `fieldName: *"default" | "other"` (concrete, no `?` marker). When Optional() is set without a Default, the `?` marker must appear.

*   ArrayParam.RequiredImports() must return ["list"] when MinItems or MaxItems is set, and nil when neither is set. If both are set, ["list"] is returned exactly once. The CUE generator must auto-import "list" based on this method.

*   StringKeyMapParam must expose HasKey(key string) Condition, IsEmpty() Condition, IsNotEmpty() Condition, LenEq(n int) Condition, and LenGt(n int) Condition methods mirroring MapParam's predicates.

*   StringKeyMapParam.HasKey(key) must return a *MapHasKeyCondition with ParamName() returning the parameter name and Key() returning the key argument.

*   When SetIf uses a bracket-access path (e.g. "data[key-name]") with a single condition, the bracket key must be rendered inside a guarded if block. With an AbsentOrEmpty condition, two if blocks are emitted (one per branch). An unconditional Set with a bracket-access path must render without any if block.

*   A bracket-access path that has nested children (e.g. "metadata.annotations[key].nested") must render as a quoted struct: `"key": { nested: ... }`.

*   FailWhen(coll.IsEmpty()) in a validator must emit two false-assignment blocks (one per AbsentOrEmpty branch). OnlyWhen(coll.IsEmpty()) must duplicate the entire validator body under both branches.


*   Interface details: Type: Method
Name: RequiredImports
Location: pkg/definition/defkit/param.go
Signature: (p *ArrayParam) RequiredImports() []string
Description: Returns the CUE imports required by this array parameter's constraints. Returns ["list"] when MinItems or MaxItems is set; returns nil otherwise.

Type: Method
Name: HasKey
Location: pkg/definition/defkit/param.go
Signature: (p *StringKeyMapParam) HasKey(key string) Condition
Description: Creates a condition that checks if this string-keyed map parameter contains a specific key. Must return *MapHasKeyCondition.

Type: Method
Name: LenEq
Location: pkg/definition/defkit/param.go
Signature: (p *StringKeyMapParam) LenEq(n int) Condition
Description: Creates a condition that checks if this string-keyed map has exactly n entries. When n == 0, returns *AbsentOrEmptyCondition (equivalent to IsEmpty()).

Type: Method
Name: LenGt
Location: pkg/definition/defkit/param.go
Signature: (p *StringKeyMapParam) LenGt(n int) Condition
Description: Creates a condition that checks if this string-keyed map has more than n entries.

Type: Method
Name: IsEmpty
Location: pkg/definition/defkit/param.go
Signature: (p *StringKeyMapParam) IsEmpty() Condition
Description: Creates a condition that checks if this string-keyed map is absent or empty. Returns *AbsentOrEmptyCondition.

Type: Method
Name: IsNotEmpty
Location: pkg/definition/defkit/param.go
Signature: (p *StringKeyMapParam) IsNotEmpty() Condition
Description: Creates a condition that checks if this string-keyed map is set and non-empty.

Type: Struct
Name: MapHasKeyCondition
Location: pkg/definition/defkit/expr.go
Description: Exported condition type representing a map-key existence check. Must have ParamName() string and Key() string methods. StringKeyMapParam.HasKey() must return this type.
Signature: ParamName() string; Key() string

Type: Struct
Name: AbsentOrEmptyCondition
Location: pkg/definition/defkit/expr.go
Description: Exported condition type representing "parameter is absent OR empty". At render time, expands into two separate if blocks: one for the absent branch (`parameter["name"] == _|_`) and one for the set-and-empty branch (`parameter["name"] != _|_ if len(parameter["name"]) == 0`). Has a Branches() []Condition method returning these two branches. ArrayParam.IsEmpty(), MapParam.IsEmpty(), and LenEq(0) on both must return this type.
Signature: ParamName() string; Branches() []Condition


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.