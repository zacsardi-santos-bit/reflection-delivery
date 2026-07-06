I'm working on the flow update logic in our workflow engine.

*   The FlowService.findUnchangedTrigger(Flow flow, Flow previous) static method must return a list of AbstractTrigger objects that exist in both flow and previous with matching ids and identical configurations (deep equality). If a trigger exists in both revisions with the same id and all fields equal, it must appear in the returned list.

*   FlowService.findUnchangedTrigger must return an empty list when a trigger has the same id in both revisions but differs in any configuration field (e.g., a changed cron expression).

*   FlowService.findUnchangedTrigger must return an empty list when a trigger exists in the current flow but does not have a corresponding entry (matching by id) in the previous flow.

*   When a flow is updated and a trigger remains completely unchanged between the old and new revision, the flow update process must emit a TriggerUpdated event (an instance of io.kestra.core.scheduler.events.TriggerUpdated, which extends TriggerEvent) to the TriggerEventQueue for that trigger.

*   The TriggerUpdated event emitted for an unchanged trigger must have an id whose getTriggerId() value equals the id of the unchanged trigger.


*   Interface details: Type: Method (static)
Name: findUnchangedTrigger
Location: core/src/main/java/io/kestra/core/services/FlowService.java
Signature: public static List<AbstractTrigger> findUnchangedTrigger(Flow flow, Flow previous)
Description: Compares the triggers of two flow revisions and returns a list of triggers that are present in both `flow` and `previous` with the same id and identical configuration (deep reflection equality). Returns an empty list if no triggers are unchanged between the two revisions, if the current revision contains only new triggers not found in `previous`, or if triggers share the same id but have differing configuration values. The method lives in the class `io.kestra.core.services.FlowService`.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.