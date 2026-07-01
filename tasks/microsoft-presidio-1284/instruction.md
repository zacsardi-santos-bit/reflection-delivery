Implement runtime support for registering and removing custom operators in the Presidio anonymizer and deanonymizer engines, ensuring each engine instance maintains its own independent operator registry. Inject the entity type into operator parameters during validation and execution.

* Refactor `OperatorsFactory`:
    * Use per-instance state for operator registries.
    * Implement instance methods `get_anonymizers()` and `get_deanonymizers()` to return the current registry as a dictionary mapping operator names to classes.
    * Implement `add_anonymize_operator(operator: Type[Operator])` and `add_deanonymize_operator(operator: Type[Operator])` to register operators by name.
    * Implement `remove_anonymize_operator(operator: Type[Operator])` and `remove_deanonymize_operator(operator: Type[Operator])`.
        * Raise `InvalidParamException` with the message 'Operator {operator_name} not found in anonymizers list' or 'Operator {operator_name} not found in deanonymizers list' if the operator is not found.

* Update `AnonymizerEngine`:
    * Implement `add_anonymizer(anonymizer_cls: Type[Operator])` to register operators with the engine's factory.
    * Implement `remove_anonymizer(anonymizer_cls: Type[Operator])` to remove operators from the engine's factory.

* Update `DeanonymizeEngine`:
    * Implement `add_deanonymizer(deanonymizer_cls: Type[Operator])` to register operators with the engine's factory.
    * Implement `remove_deanonymizer(deanonymizer_cls: Type[Operator])` to remove operators from the engine's factory.

* Ensure entity type injection:
    * Inject `entity_type` into the operator's parameters dictionary before calling `validate()` and `operate()`.

* Modify `EngineBase._operate` method:
    * Use parameter names `pii_entities`, `operators_metadata`, and `operator_type` for the respective arguments.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.