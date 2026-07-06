I noticed that when just-in-time context mode is turned on, the project memory file content ends up being sent to the model twice — once as part of the system instructions, and again inside the environment context.

*   The getEnvironmentContext function must check whether JIT context is enabled before including environment memory in the returned context parts.

*   When JIT context is enabled (isJitContextEnabled returns true), getEnvironmentContext must not include environment memory content in parts[0].text and must not call getEnvironmentMemory at all.

*   When JIT context is disabled (isJitContextEnabled returns false), getEnvironmentContext must include the environment memory content in parts[0].text, preserving the existing behavior.


*   Interface details: Type: Function
Name: getEnvironmentContext
Location: packages/core/src/utils/environmentContext.ts
Signature: getEnvironmentContext(config: Config) -> Promise<Part[]>
Description: Builds environment context parts to send to the model. Must check config.isJitContextEnabled() — if it returns true, must skip calling config.getEnvironmentMemory() and must not include its content in the returned parts. If isJitContextEnabled() returns false (or is not set), must include the result of config.getEnvironmentMemory() in the context as before.

Type: Interface method (on Config)
Name: isJitContextEnabled
Location: packages/core/src/config.ts (or equivalent Config interface definition)
Signature: isJitContextEnabled(): boolean
Description: Returns true when just-in-time context mode is active. Used by getEnvironmentContext to determine whether to include environment memory in the context parts.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.