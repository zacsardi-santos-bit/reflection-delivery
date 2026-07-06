I'm using the random sample filter to run a subset of my test cases, but every time I run an evaluation I get a different selection of tests.

*   The FilterOptions type in src/commands/eval/filterTests.ts must include an optional sampleSeed field (sampleSeed?: number). When filterTests is called with both a sample count and a sampleSeed, the function must return the same subset of tests on every invocation with the same arguments.

*   When sampleSeed is provided to filterTests, the function must use a deterministic pseudorandom number generator seeded by that value, and must not call Math.random at any point during sampling.

*   CommandLineOptionsSchema in src/types/index.ts must include an optional filterSampleSeed field that coerces string inputs to integers (e.g., '42' becomes 42), rejects non-numeric strings, rejects non-integer numbers (floats), and rejects unsafe integers (values outside the safe integer range, i.e., greater than Number.MAX_SAFE_INTEGER).

*   readConfig in src/util/config/load.ts must normalize commandLineOptions.filterSampleSeed when reading a YAML config file: a quoted numeric string like '42' must be coerced to the number 42.

*   readConfig must reject configuration files where commandLineOptions.filterSampleSeed is an invalid value (non-numeric string, float, or unsafe integer) by throwing an error with name 'ConfigResolutionError' and a message containing 'Invalid commandLineOptions in configuration file <configPath>'.

*   resolveConfigs in src/util/config/load.ts must apply seeded sampling independently to each scenario when filterSample and filterSampleSeed are both configured. Calling resolveConfigs twice with the same inputs must produce identical test selections for each scenario.

*   When multiple scenarios are present and filterSampleSeed is configured, each scenario must receive a distinct derived seed based on its index, so that different scenarios do not select the same subset of tests.

*   resolveConfigs must not mutate the original scenario tests arrays or the original scenarios config array. After calling resolveConfigs, a scenario that originally had a file reference string (e.g., 'file://tests.yaml') as its tests value must still hold that string reference unchanged.

*   The eval command must accept a --filter-sample-seed <number> CLI flag. When used together with --filter-sample <number>, repeated invocations with the same seed must select the same tests and produce identical output. The command must exit with code 0 on success.

*   When commandLineOptions.filterSample and commandLineOptions.filterSampleSeed are set in a config file, running doEval twice with that config must produce the same test selection each time.


*   Interface details: Type: Interface
Name: FilterOptions
Location: src/commands/eval/filterTests.ts
Description: Options for the filterTests function. Must include an optional sampleSeed field for deterministic sampling.
Signature: { sampleSeed?: number }
Note: This is an existing interface that must be extended with the new optional field `sampleSeed?: number`.

Type: Schema field
Name: filterSampleSeed
Location: src/types/index.ts (within CommandLineOptionsSchema)
Description: Optional field on CommandLineOptionsSchema that accepts a safe integer for repeatable sampling. Must coerce string inputs to numbers and reject non-numeric strings, floats, and unsafe integers (beyond Number.MAX_SAFE_INTEGER).
Signature: z.coerce.number().int().safe().optional()

Type: Function
Name: readConfig
Location: src/util/config/load.ts
Description: Existing function that reads and parses a config file. Must validate and normalize commandLineOptions.filterSampleSeed — coercing quoted numeric strings to numbers, and throwing a ConfigResolutionError (name: 'ConfigResolutionError') with a message containing 'Invalid commandLineOptions in configuration file <configPath>' for invalid values (non-numeric strings, floats, unsafe integers).

Type: Function
Name: resolveConfigs
Location: src/util/config/load.ts
Description: Existing function that resolves the final configuration. When filterSample and filterSampleSeed are configured (from cmdObj or from defaultConfig.commandLineOptions), must apply seeded sampling independently to each scenario using a per-scenario derived seed (based on the scenario's index). Must not mutate the original scenario tests arrays or the original scenarios array (external file references like 'file://tests.yaml' must remain intact in the source object).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.