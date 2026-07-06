I'm working on Airflow's partitioned asset scheduling and I need to add support for configurable wait policies on rollup mappers.

*   WaitPolicy must be an abstract base class located at airflow/partition_mappers/wait_policy.py with an abstract method is_satisfied_by_keys(matched: set, expected: set) -> PartitionSatisfaction. The default base implementation must compute the intersection count of matched and expected, call is_satisfied(matched_count, expected_count), and wrap the result in a PartitionSatisfaction.

*   WaitForAll must be a concrete subclass of WaitPolicy at airflow/partition_mappers/wait_policy.py. It must be stateless (no constructor parameters), support equality and hashing so that two instances compare equal and hash equal, repr as 'WaitForAll()', implement is_satisfied(matched: int, expected: int) -> bool returning True iff matched == expected, and implement is_unreachable(expected: int) -> bool that always returns False. Its is_satisfied_by_keys override must return PartitionSatisfaction(satisfied=True, ...) when matched is a superset of expected.

*   MinimumCount must be a concrete subclass of WaitPolicy at airflow/partition_mappers/wait_policy.py. Constructor takes a single integer n and stores it as attribute .n. Passing n=0 must raise ValueError with a message matching 'MinimumCount\(0\) is degenerate'. Repr must be 'MinimumCount(n=5)' for n=5 and 'MinimumCount(n=-3)' for n=-3. Equality and hash must be based on n.

*   MinimumCount.is_satisfied(matched: int, expected: int) -> bool must: for positive n, return True iff matched >= n; for negative n, return True iff matched >= max(0, expected + n). Edge case: MinimumCount(5).is_satisfied(5, 4) returns True when window is smaller than threshold.

*   MinimumCount.is_unreachable(expected: int) -> bool must return True iff n is positive and n > expected (the threshold can never be reached given the window size). For negative n, must always return False.

*   PartitionSatisfaction must be a class (dataclass or equivalent) at airflow/partition_mappers/wait_policy.py with fields satisfied: bool, unreachable: bool, and unreachable_reason: Optional[str]. Construction must raise ValueError with message matching 'unreachable_reason must be set' when unreachable=True and unreachable_reason is None. Construction must raise ValueError with message matching 'unreachable_reason must be None' when unreachable=False and unreachable_reason is not None.

*   WaitPolicy.is_satisfied_by_keys must return a PartitionSatisfaction where, when unreachable is True, unreachable_reason contains both str(len(expected_keys)) and repr(policy). The unreachable reason format must be: 'wait policy {policy!r} can never be satisfied given the window\'s cardinality {len(expected_keys)}'.

*   WaitForAll and MinimumCount must also be available as lightweight SDK variants at airflow/sdk/definitions/partition_mappers/wait_policy.py. These SDK variants must support construction, equality, hashing, and repr matching the same format as the core variants. MinimumCount(0) must also raise ValueError with the same message.

*   MinimumCount must be exported from airflow.sdk (importable as 'from airflow.sdk import MinimumCount').

*   RollupMapper must accept a wait_policy keyword argument defaulting to WaitForAll(). The old allow_missing and minimum_count parameters must be replaced by wait_policy. The encoded wire format for a RollupMapper with default WaitForAll must contain a wait_policy field with Encoding.TYPE ending in 'WaitForAll' and Encoding.VAR equal to {}, and must NOT contain allow_missing or minimum_count fields.

*   encode_wait_policy(policy: WaitPolicy) must be available at airflow/serialization/encoders.py. For WaitForAll(), it must return a dict with Encoding.VAR equal to {}. For MinimumCount(n), it must return a dict with Encoding.VAR equal to {'n': n}. Non-builtin WaitPolicy subclasses must raise WaitPolicyNotSupported (from airflow.serialization.helpers).

*   decode_partition_mapper and encode_partition_mapper must perform correct round-trip serialization of RollupMapper including the wait_policy field. A RollupMapper with MinimumCount(5) or MinimumCount(-3) or WaitForAll() as wait_policy must survive encode → decode and produce equal policy instances.

*   SchedulerJobRunner must have a _partition_unreachable_seen: set attribute and a _warn_unreachable_asset_partition(apdr, name, uri, reason) method that logs a warning with format 'Rollup asset (name=%r, uri=%r) on Dag %r is permanently unreachable: %s' and deduplicates per (target_dag_id, name, uri) tuple — the warning must fire exactly once for repeated calls with the same tuple.

*   SchedulerJobRunner._resolve_asset_partition_status must return False (not trigger a dag run) when the wait policy is permanently unreachable, and must call _warn_unreachable_asset_partition with the appropriate reason string.

*   The scheduler must create a dag run for a RollupMapper with MinimumCount(5) only after exactly 5 keys arrive (not 4), and for MinimumCount(-3) on a 60-key window only after 57 keys arrive (not 56). Exception handling in WaitPolicy.is_satisfied_by_keys must prevent dag run creation and must be patchable at WaitPolicy.is_satisfied_by_keys (not at an older internal helper method).


*   Interface details: Type: Class
Name: WaitPolicy
Location: airflow-core/src/airflow/partition_mappers/wait_policy.py
Description: Abstract base class for rollup wait policies. Provides a default implementation of is_satisfied_by_keys that converts key sets to counts and delegates to is_satisfied. Subclasses must implement is_satisfied.
Signature:
  is_satisfied(matched: int, expected: int) -> bool  # abstract
  is_unreachable(expected: int) -> bool  # abstract
  is_satisfied_by_keys(matched: set, expected: set) -> PartitionSatisfaction  # concrete default

Type: Class
Name: WaitForAll
Location: airflow-core/src/airflow/partition_mappers/wait_policy.py
Description: Concrete WaitPolicy that fires only when all expected partition keys are present. Stateless; two instances are equal and have the same hash. Overrides is_satisfied_by_keys for short-circuit behavior.
Signature:
  __repr__() -> str  # returns "WaitForAll()"
  __eq__(other) -> bool
  __hash__() -> int
  is_satisfied(matched: int, expected: int) -> bool  # True iff matched == expected
  is_unreachable(expected: int) -> bool  # always False
  is_satisfied_by_keys(matched: set, expected: set) -> PartitionSatisfaction

Type: Class
Name: MinimumCount
Location: airflow-core/src/airflow/partition_mappers/wait_policy.py
Description: Concrete WaitPolicy parameterized by an integer n. Positive n: fires when matched >= n. Negative n: fires when matched >= max(0, expected + n). Raises ValueError for n=0.
Signature:
  __init__(n: int) -> None  # raises ValueError if n == 0 (message: "MinimumCount(0) is degenerate")
  n: int  # stored attribute
  __repr__() -> str  # returns "MinimumCount(n=<value>)"
  __eq__(other) -> bool
  __hash__() -> int
  is_satisfied(matched: int, expected: int) -> bool
  is_unreachable(expected: int) -> bool  # True iff n > 0 and n > expected

Type: Class
Name: PartitionSatisfaction
Location: airflow-core/src/airflow/partition_mappers/wait_policy.py
Description: Result type returned by is_satisfied_by_keys. Enforces cross-field invariant at construction time.
Signature:
  __init__(satisfied: bool, unreachable: bool, unreachable_reason: Optional[str]) -> None
    # raises ValueError("unreachable_reason must be set") if unreachable=True and unreachable_reason is None
    # raises ValueError("unreachable_reason must be None") if unreachable=False and unreachable_reason is not None
  satisfied: bool
  unreachable: bool
  unreachable_reason: Optional[str]

Type: Class
Name: WaitForAll
Location: task-sdk/src/airflow/sdk/definitions/partition_mappers/wait_policy.py
Description: Lightweight SDK-side variant of WaitForAll. No scheduling logic — only construction, equality, hashing, and repr.
Signature:
  __repr__() -> str  # returns "WaitForAll()"
  __eq__(other) -> bool
  __hash__() -> int

Type: Class
Name: MinimumCount
Location: task-sdk/src/airflow/sdk/definitions/partition_mappers/wait_policy.py
Description: Lightweight SDK-side variant of MinimumCount. Stores n; raises ValueError for n=0; supports equality, hashing, repr.
Signature:
  __init__(n: int) -> None  # raises ValueError if n == 0 (message: "MinimumCount(0) is degenerate")
  n: int
  __repr__() -> str  # returns "MinimumCount(n=<value>)"
  __eq__(other) -> bool
  __hash__() -> int

Type: Function
Name: encode_wait_policy
Location: airflow-core/src/airflow/serialization/encoders.py
Description: Serializes a WaitPolicy instance to a dict. For WaitForAll returns {Encoding.VAR: {}}. For MinimumCount(n) returns {Encoding.VAR: {"n": n}}. Non-builtin WaitPolicy subclasses raise WaitPolicyNotSupported.
Signature: encode_wait_policy(policy: WaitPolicy) -> dict

Type: Class
Name: WaitPolicyNotSupported
Location: airflow-core/src/airflow/serialization/helpers.py
Description: Exception raised when encode_wait_policy or encode_partition_mapper encounters a WaitPolicy subclass that is not a built-in type (WaitForAll or MinimumCount).

Type: Method
Name: _warn_unreachable_asset_partition
Location: airflow-core/src/airflow/jobs/scheduler_job_runner.py (SchedulerJobRunner class)
Description: Logs a warning about a permanently unreachable rollup asset partition and deduplicates per (target_dag_id, name, uri) tuple using the _partition_unreachable_seen set attribute. Warning format: "Rollup asset (name=%r, uri=%r) on Dag %r is permanently unreachable: %s".
Signature: _warn_unreachable_asset_partition(self, apdr, name: str, uri: str, reason: str) -> None

Note: SchedulerJobRunner must also have a _partition_unreachable_seen: set attribute and an updated _resolve_asset_partition_status method. The unreachable reason string format is: "wait policy {policy!r} can never be satisfied given the window's cardinality {cardinality}".

Note: MinimumCount must be exported from airflow.sdk (i.e., available as "from airflow.sdk import MinimumCount").


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.