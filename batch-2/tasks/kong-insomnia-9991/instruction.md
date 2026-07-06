I'm working on improving how API linting rulesets that reference remote sources are handled.

*   The isPrivateOrLoopbackHost function must return true for IPv4 unspecified (0.0.0.0/8) addresses, including both 0.0.0.0 and addresses like 0.255.255.255.

*   The bundleSpectralRuleset function's error message for a non-https remote extends entry must contain the substring 'must use https'.

*   A new exported function compileSpectralRulesetFromContent must be added to packages/insomnia/src/common/bundle-spectral-ruleset.ts. It accepts a YAML string as input and returns a Promise resolving to a compiled YAML string where all remote extends URLs have been fetched and fully inlined. The compiled output must not contain the original remote URLs.

*   compileSpectralRulesetFromContent must recursively fetch and inline nested remote extends (i.e., a remote ruleset that itself extends another remote URL must be fully resolved).

*   compileSpectralRulesetFromContent must preserve built-in identifiers such as spectral:oas that appear in remote rulesets — these are passed through into the compiled output without fetching.

*   compileSpectralRulesetFromContent must reject a remote ruleset that declares a 'functions' key by throwing an error containing the substring 'failed validation'.

*   compileSpectralRulesetFromContent must reject a non-https remote extends URL by throwing an error containing the substring 'must use https', without making any network request.

*   compileSpectralRulesetFromContent must reject a remote extends URL whose host resolves to a loopback address by throwing an error containing the substring 'disallowed host', without making any network request.

*   compileSpectralRulesetFromContent must reject input YAML that is not a mapping object at the top level by throwing an error containing the substring 'must be an object at the top level'.

*   A new module must be created at packages/insomnia/src/main/spectral-ruleset-cache.ts exporting the functions compiledRulesetPathFor, writeCompiledRuleset, and deleteCompiledRuleset.

*   compiledRulesetPathFor(projectId: string): string must return path.join(dataPath, 'projects', projectId, '.spectral.yaml'). The dataPath must be taken from the INSOMNIA_DATA_PATH environment variable when set; otherwise it must use the electron app's userData path.

*   writeCompiledRuleset(projectId: string, content: string): Promise<{ compiledPath: string }> must: call compileSpectralRulesetFromContent with the given content; create the directory at path.dirname(compiledPath) with { recursive: true } before writing; write the compiled result to compiledPath with utf8 encoding; return { compiledPath } where compiledPath equals compiledRulesetPathFor(projectId); propagate any error thrown by compileSpectralRulesetFromContent.

*   writeCompiledRuleset must implement content-hash caching: if called with the same content for the same project and the compiled file still exists on disk (verified via fs.promises.access), it must skip recompilation and the file write entirely. If the file has been deleted externally (access throws ENOENT), it must recompile and rewrite. If the content changes, it must recompile and rewrite.

*   deleteCompiledRuleset(projectId: string): Promise<void> must remove the project's compiled directory by calling fs.promises.rm on path.dirname(compiledRulesetPathFor(projectId)) with { recursive: true, force: true }, and must clear the in-memory content-hash cache for that project so the next writeCompiledRuleset call always recompiles.


*   Interface details: Type: Function
Name: isPrivateOrLoopbackHost
Location: packages/insomnia/src/common/private-host.ts
Signature: isPrivateOrLoopbackHost(host: string): boolean
Description: Returns true if the given host is a loopback address, private IP range, link-local address, or unspecified (0.0.0.0/8) address. Must now also return true for addresses in the 0.0.0.0/8 range (0.0.0.0 through 0.255.255.255).

Type: Function
Name: compileSpectralRulesetFromContent
Location: packages/insomnia/src/common/bundle-spectral-ruleset.ts
Signature: compileSpectralRulesetFromContent(content: string): Promise<string>
Description: Accepts a YAML ruleset string, fetches and recursively inlines all remote extends URLs into the content, and returns a compiled YAML string with no remote URL references. Built-in identifiers (e.g. spectral:oas) are preserved as-is. Throws an error containing 'must use https' for non-https remote URLs (without fetching). Throws an error containing 'disallowed host' for loopback/private/unspecified hosts (without fetching). Throws an error containing 'failed validation' if a remote ruleset declares a 'functions' key. Throws an error containing 'must be an object at the top level' if the input YAML is not a mapping object.

Type: Function
Name: compiledRulesetPathFor
Location: packages/insomnia/src/main/spectral-ruleset-cache.ts
Signature: compiledRulesetPathFor(projectId: string): string
Description: Returns the absolute path where the compiled ruleset for a given project is stored: path.join(dataPath, 'projects', projectId, '.spectral.yaml'). Uses process.env.INSOMNIA_DATA_PATH for dataPath when set; otherwise uses the electron app's userData path (app.getPath('userData')).

Type: Function
Name: writeCompiledRuleset
Location: packages/insomnia/src/main/spectral-ruleset-cache.ts
Signature: writeCompiledRuleset(projectId: string, content: string): Promise<{ compiledPath: string }>
Description: Compiles the given YAML content via compileSpectralRulesetFromContent, creates the project directory (with { recursive: true }), and writes the compiled result to compiledRulesetPathFor(projectId) with utf8 encoding. Returns { compiledPath } where compiledPath === compiledRulesetPathFor(projectId). Implements content-hash caching: skips recompilation and write if content is unchanged and the file exists on disk (checked via fs.promises.access); recompiles if the file has been deleted (access throws ENOENT) or if content changes. Propagates errors from compileSpectralRulesetFromContent.

Type: Function
Name: deleteCompiledRuleset
Location: packages/insomnia/src/main/spectral-ruleset-cache.ts
Signature: deleteCompiledRuleset(projectId: string): Promise<void>
Description: Removes the project's compiled ruleset directory by calling fs.promises.rm on path.dirname(compiledRulesetPathFor(projectId)) with { recursive: true, force: true }. Also clears the in-memory content-hash cache entry for the given projectId so the next writeCompiledRuleset call always triggers a full recompile.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.