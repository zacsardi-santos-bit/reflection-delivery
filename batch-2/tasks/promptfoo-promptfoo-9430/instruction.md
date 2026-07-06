I'm seeing duplicate providers in my combined config output when I reuse the same provider object across multiple places.

*   The combineConfigs function must deduplicate provider entries when the exact same provider object reference appears more than once in the providers array, even when that object contains function-valued properties (such as a transform callback). The output providers list must contain only one entry for the repeated reference.

*   The combineConfigs function must deduplicate ApiProvider class instances when the exact same instance reference appears more than once in the providers array. The output providers list must contain only one entry for the repeated instance.

*   Different provider objects (distinct references) that each contain function-valued fields must NOT be deduplicated — they represent distinct configurations and must each appear in the output.

*   Different ApiProvider class instances (distinct instances, even of the same class) must NOT be deduplicated — only identical references must be collapsed.


*   Interface details: Type: Function
Name: combineConfigs
Location: src/util/config/load.ts
Signature: combineConfigs(configPaths: string[]): Promise<UnifiedConfig>
Description: Merges one or more promptfoo config files into a single unified config. When building the combined providers list, it must deduplicate entries where the same provider reference (string, function, plain object, or ApiProvider instance) appears more than once. Provider objects that contain function-valued properties must be deduplicated by reference when the same object reference is repeated, rather than being blindly appended. Different object references — even if structurally similar — must still be preserved as separate entries.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.