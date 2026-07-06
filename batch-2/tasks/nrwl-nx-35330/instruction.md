I'm working on an Nx workspace and trying to migrate from the legacy ESLint configuration format to the new flat config format using the built-in conversion tool.

*   The convertEslintJsonToFlatConfig function must NOT inject a hardcoded default ignore block for '**/dist' and '**/out-tsc' into the generated flat config output.

*   When converting to ESM (mjs) format, parser references in overrides must be emitted as a static top-level import statement (e.g., 'import jsoncEslintParser from "jsonc-eslint-parser"') with the local binding referenced inline ('parser: jsoncEslintParser') rather than as a dynamic await import expression. The local binding name must be the camelCase property name derived from the package name (e.g., 'jsonc-eslint-parser' → 'jsoncEslintParser').

*   When ignorePatterns contains a combination of a broad pattern and a negated sub-pattern (e.g., 'dist/**' paired with '!dist/keep.js'), both the broad pattern and the negation must be preserved in the output. Only meaningless cascading patterns ('**/*', '!**/*') and 'node_modules' are filtered out.

*   When the extends array contains a local relative path to an extensionless '.eslintrc' file (e.g., '../../.eslintrc'), convertEslintJsonToFlatConfig must recognize it as a JSON config, generate a static import (e.g., 'import baseConfig from "../../eslint.config.mjs"'), spread it ('...baseConfig'), and must NOT use compat.extends for that reference.

*   convertToFlatConfigGenerator must rewrite all string input entries in targetDefaults and namedInputs in nx.json that reference legacy '.eslintrc.json', '.eslintrc.base.json', or '.eslintignore' filenames to the corresponding flat config filenames: '.eslintrc.json' → 'eslint.config.{format}', '.eslintrc.base.json' → 'eslint.base.config.{format}', '.eslintignore' → 'eslint.config.{format}'.

*   After rewriting, duplicate string entries within the same input array must be deduplicated (e.g., if both '.eslintrc.json' and '.eslintignore' rewrite to the same flat config path, only one entry is kept).

*   Non-string input entries (e.g., objects like { runtime: 'node --version' }) in nx.json targetDefaults inputs must be left untouched during the rewrite.

*   convertToFlatConfigGenerator must also rewrite stale legacy references in every project's targets inputs and namedInputs arrays (in their individual project configuration files), applying the same rename and deduplication logic.

*   Negation prefixes on input strings (e.g., '!{projectRoot}/.eslintrc.json') must be preserved through the rename so the result is '!{projectRoot}/eslint.config.{format}'.


*   Interface details: Type: Function
Name: convertEslintJsonToFlatConfig
Location: packages/eslint/src/generators/convert-to-flat-config/converters/json-converter.ts
Signature: convertEslintJsonToFlatConfig(tree: Tree, root: string, config: ESLint.ConfigData, projects: string[], format: 'mjs' | 'cjs') -> { content: string, ... }
Description: Converts a legacy ESLint JSON config object to a flat config file. Must no longer inject a hardcoded ignores block for '**/dist'/'**/out-tsc'. Must preserve negated ignorePatterns paired with broader patterns. Must treat extensionless '.eslintrc' paths in extends the same as '.eslintrc.json'. For ESM format, parser references in overrides must be emitted as static top-level imports with a camelCase local binding name derived from the package name (e.g., 'jsonc-eslint-parser' → local binding 'jsoncEslintParser').

Type: Function
Name: convertToFlatConfigGenerator
Location: packages/eslint/src/generators/convert-to-flat-config/generator.ts
Signature: convertToFlatConfigGenerator(tree: Tree, options: ConvertToFlatConfigGeneratorSchema) -> Promise<void>
Description: Nx generator that converts all ESLint configs in a workspace to flat config format. In addition to converting config files, must now rewrite stale '.eslintrc[.base].json' and '.eslintignore' filename references in targetDefaults inputs and namedInputs in nx.json, and in targets inputs and namedInputs in every project's configuration file. Renaming rules: '.eslintrc.json' → 'eslint.config.{format}', '.eslintrc.base.json' → 'eslint.base.config.{format}', '.eslintignore' → 'eslint.config.{format}'. Negation prefixes (e.g., '!') must be preserved. After renaming, duplicate string entries within the same array must be deduplicated. Non-string input entries (e.g., runtime/env objects) must be left unchanged.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.