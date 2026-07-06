I'd like to be able to attach key-value tags to an evaluation run directly from the command line — things like the environment name or a CI run identifier — without having to modify my configuration file each time.

*   The doEval function's first parameter (cmdObj) must accept an optional 'tags' field of type Record<string, string> representing runtime tags for the evaluation.

*   When doEval is invoked with runtime tags (cmdObj.tags), the final tags applied to both the eval record's config.tags and the test suite's tags must be the result of merging in priority order: base config.tags (lowest priority) → commandLineOptions.tags (medium priority) → cmdObj.tags (highest priority). Tags from higher-priority sources override matching keys from lower-priority sources.

*   When doEval is called with cmdObj.tags set (non-empty) and cmdObj.resume is also set, the function must fail with the exact error message: 'Cannot use --tag with --resume. Resumed evaluations keep their original tags.'

*   When doEval is called with cmdObj.tags set (non-empty) and cmdObj.retryErrors is also set, the function must fail with the exact error message: 'Cannot use --tag with --retry-errors. Retried evaluations keep their original tags.'

*   The evalCommand function must register a '--tag <key=value>' CLI option that can be specified multiple times; each occurrence is parsed as a key=value pair and accumulated into a single tags dictionary. The accumulated tags dictionary must be passed to the config resolution layer as the 'tags' property.

*   The CommandLineOptions type (and its associated schema in src/types/index.ts) must include an optional 'tags' field of type Record<string, string>.


*   Interface details: Type: Function
Name: doEval
Location: src/commands/eval.ts
Signature: doEval(cmdObj: Partial<EvalCommandOptions>, config: UnifiedConfig, configPath: string | undefined, options: Record<string, any>) -> Promise<Eval>
Description: Runs an evaluation. The cmdObj parameter must support an optional 'tags' field of type Record<string, string>. When tags are provided, they are merged over the resolved config's tags (with commandLineOptions.tags applied first, then cmdObj.tags on top). The merged tags are written to both the eval record's config.tags and the testSuite.tags. Calling with tags and resume set simultaneously, or with tags and retryErrors set simultaneously, must result in a failure.

Type: Function
Name: evalCommand
Location: src/commands/eval.ts
Signature: evalCommand(program: Command, defaultConfig: Partial<UnifiedConfig>, defaultConfigPath: string | undefined) -> Command
Description: Registers the 'eval' subcommand with Commander. Must add a '--tag <key=value>' option that can be specified multiple times (each occurrence is parsed as key=value and accumulated into a Record<string, string>). The accumulated tags dictionary must be forwarded to the config resolution step as the 'tags' property.

Type: Schema/Type
Name: CommandLineOptionsSchema
Location: src/types/index.ts
Signature: CommandLineOptionsSchema extends Zod schema object with optional field: tags: z.record(z.string(), z.string()).optional()
Description: The Zod schema defining all recognized command-line options. Must include an optional 'tags' field of type Record<string, string> so that runtime tags are validated and typed consistently across the codebase.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.