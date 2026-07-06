I'm working with Airflow's template field serialization and I've noticed that one of the sentinel objects used to represent "not set" template fields doesn't produce a human-readable string when converted.

*   The ArgNotSet class in airflow.serialization.definitions.notset must implement __repr__ and __str__ methods that both return the string "NOTSET".

*   The NOTSET singleton (an instance of ArgNotSet) must produce "NOTSET" when converted to a string or when its repr is taken.

*   The SET_DURING_EXECUTION sentinel (from airflow.sdk.definitions._internal.types) must produce "DYNAMIC (set during execution)" for both repr() and str() calls.

*   serialize_template_field(NOTSET, field_name) must return the string "NOTSET" for any field_name argument.

*   serialize_template_field(SET_DURING_EXECUTION, field_name) must return the string "DYNAMIC (set during execution)" for any field_name argument.


*   Interface details: Type: Class
Name: ArgNotSet
Location: airflow-core/src/airflow/serialization/definitions/notset.py
Description: Sentinel class representing a "not set" value for template fields. Must implement __repr__ and __str__ returning "NOTSET".
Signature: __repr__(self) -> str  # returns "NOTSET"
           __str__(self) -> str   # returns "NOTSET"

Type: Module-level constant
Name: NOTSET
Location: airflow-core/src/airflow/serialization/definitions/notset.py
Description: Singleton instance of ArgNotSet. Must produce "NOTSET" for both repr() and str().

Type: Class
Name: SetDuringExecution
Location: airflow-core/src/airflow/sdk/definitions/_internal/types.py
Description: Sentinel class representing a "set during execution" value for template fields. Must implement __repr__ and __str__ returning "DYNAMIC (set during execution)".
Signature: __repr__(self) -> str  # returns "DYNAMIC (set during execution)"
           __str__(self) -> str   # returns "DYNAMIC (set during execution)"

Type: Module-level constant
Name: SET_DURING_EXECUTION
Location: airflow-core/src/airflow/sdk/definitions/_internal/types.py
Description: Singleton instance of SetDuringExecution. Must produce "DYNAMIC (set during execution)" for both repr() and str().

Type: Function
Name: serialize_template_field
Location: airflow-core/src/airflow/serialization/helpers.py
Description: Serializes a template field value to a string. Must return "NOTSET" when given the NOTSET sentinel and "DYNAMIC (set during execution)" when given the SET_DURING_EXECUTION sentinel.
Signature: serialize_template_field(value: Any, field_name: str) -> str


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.