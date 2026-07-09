I'm working on Insomnia's OpenAPI linting feature and need to add proper support for custom Spectral rulesets across all project types.

*   isPrivateOrLoopbackHost must return true for the exact hostname 'localhost' and for any subdomain of localhost (e.g., 'app.localhost', 'foo.bar.localhost').

*   isPrivateOrLoopbackHost must return true for any IPv4 address in the loopback range 127.0.0.0/8 (e.g., 127.0.0.1, 127.255.255.255).

*   isPrivateOrLoopbackHost must return true for the IPv6 loopback address '::1', including when passed in bracket notation '[::1]'.

*   isPrivateOrLoopbackHost must return true for IPv4 private ranges: 10.0.0.0/8, 172.16.0.0/12 (172.16.x.x through 172.31.x.x), 192.168.0.0/16, and link-local 169.254.0.0/16.

*   isPrivateOrLoopbackHost must return true for IPv6 private addresses in the fc00::/7 range (e.g., fc00::1, fd00::1).

*   isPrivateOrLoopbackHost must return false for public IPv4 addresses (e.g., 93.184.216.34, 8.8.8.8, 1.1.1.1), public IPv6 addresses, public hostnames (e.g., example.com), non-IP non-localhost strings, and empty string.

*   toArray must return [] for undefined, wrap a single non-array value in a one-element array, and return arrays unchanged.

*   validateSpectralRuleset must return { isValid: false, error } where error matches /empty/i for empty strings or whitespace-only content.

*   validateSpectralRuleset must return { isValid: false, error } where error matches /yaml|json/i for content that cannot be parsed as YAML, matches /object/i for YAML that parses to a non-object (array, string, null), and matches /declare at least one/i for an empty object.

*   validateSpectralRuleset must return { isValid: false, error } where error matches /unsupported top-level/i for top-level keys not in the allowed set (e.g., 'functions' is not allowed). Allowed top-level keys include at least: extends, rules.

*   validateSpectralRuleset must return { isValid: false, error } where error matches /must be strings/i for non-string entries in the extends array; error matches '"rules" must be an object' (exact substring) when the rules value is not an object; error matches /not allowed/i for rule names '__proto__', 'constructor', or 'prototype' when the rule body is an object.

*   validateSpectralRuleset must return { isValid: false, error } where error matches /must be an object, boolean, or severity string/i for rule bodies that are not objects, booleans, or valid severity strings; matches /disallowed token/i for given expressions (or any array entry in a given array) containing '__proto__', 'prototype', or 'constructor'; non-string given values are skipped (not checked).

*   validateSpectralRuleset must return { isValid: false, error } where error matches /documentationUrl/i when a string documentationUrl uses a non-https scheme (including http, ftp, javascript, or unparseable URLs). Non-string documentationUrl values are skipped.

*   validateSpectralRuleset must return { isValid: false, error } where error matches /field/i when then.field contains prototype-polluting tokens ('__proto__', 'prototype', 'constructor') or path-traversal characters ('.', '[', ']'). A plain property name like 'summary' is accepted.

*   validateSpectralRuleset must return { isValid: false, error } where error matches /not an allowed/i when then.function is not one of the built-in functions: alphabetical, casing, defined, enumeration, falsy, length, pattern, schema, truthy, typedEnum, undefined, unreferencedReusableObject, or, xor. Non-string then.function values also trigger this error. When then is an array, each entry is validated; non-object entries in a then array are skipped.

*   validateSpectralRuleset must return { isValid: true } for valid content including JSON input, built-in extends identifiers (spectral:oas, spectral:asyncapi, spectral:arazzo), a bare-string extends value, relative file path extends (./rules.yaml, ../shared/rules.yml), absolute file path extends (/tmp/rules.yaml), https URL extends, boolean and severity-string rule shorthands (true, false, 'warn', 'error'), https documentationUrl, and a full ruleset combining extends/rules/documentationUrl.

*   bundleSpectralRuleset must read the file at the given path using fs.promises.readFile and return a YAML string. For a ruleset with no extends, the output contains the rules and no 'extends' key.

*   bundleSpectralRuleset must pass built-in spectral identifier extends (spectral:oas, spectral:asyncapi, spectral:arazzo) through to the output unchanged.

*   bundleSpectralRuleset must flatten local file extends by merging child rules into the parent's rules, removing the file path from the output. When the parent and child define a rule with the same name, the parent's definition wins.

*   bundleSpectralRuleset must deduplicate spectral built-in identifiers so they appear at most once in the output, even when multiple child files reference the same identifier.

*   bundleSpectralRuleset must throw an error containing '"extends" cycle detected' when a circular extends chain is found, '"extends" nested too deeply' when nesting exceeds 5 levels deep (7 levels of nesting must throw), '"extends" target must be a .yaml or .yml file' when an extends entry points to a non-YAML file, 'tuple format' when an extends entry is an array (tuple format), and 'must be an object at the top level' when the file content is not a YAML object.

*   bundleSpectralRuleset must throw an error containing 'Invalid Spectral ruleset' when the ruleset (local or remote) declares a 'functions' key.

*   bundleSpectralRuleset must validate remote HTTPS extends by fetching the URL (using the global fetch) and running validateSpectralRuleset on the response. The remote URL must be preserved in the output extends; remote rule content must NOT be merged into the bundle. Throws 'failed validation' if the remote ruleset fails validation.

*   bundleSpectralRuleset must perform the following security checks on remote extends entries before fetching: throw an error containing 'must use https' for non-https URLs; throw an error containing 'disallowed host' for loopback or private IP hostnames (localhost, *.localhost, 127.x.x.x, [::1], 10.x.x.x, 192.168.x.x, 172.16.x.x); throw an error matching /not a valid spectral identifier|valid URL/i for entries that are not valid spectral identifiers, file paths, or URLs.

*   bundleSpectralRuleset must perform DNS resolution on remote hostname extends entries and throw an error containing 'private or loopback address' if any resolved IP address is private or loopback, without making the fetch request.

*   bundleSpectralRuleset must throw 'Failed to fetch remote' when a remote ruleset returns a non-OK HTTP response.

*   bundleSpectralRuleset must recursively validate nested remote extends within fetched remote rulesets: throw 'Remote "extends" URL must use https:' for nested http:// extends inside a remote ruleset, and throw 'failed validation' when a nested remote ruleset contains a 'functions' key.


*   Interface details: Type: Function
Name: isPrivateOrLoopbackHost
Location: packages/insomnia/src/common/private-host.ts
Signature: isPrivateOrLoopbackHost(host: string): boolean
Description: Determines whether a given hostname or IP address string is a private or loopback address. Returns true for localhost and its subdomains, IPv4 loopback (127.x.x.x), IPv6 loopback (::1 or [::1]), private IPv4 ranges (10.x.x.x, 172.16–31.x.x, 192.168.x.x), link-local IPv4 (169.254.x.x), and IPv6 private (fc00::/7 including fc00::1 and fd00::1). Returns false for public IPs, public hostnames, empty strings, and non-IP non-localhost strings.

Type: Function
Name: toArray
Location: packages/insomnia/src/common/spectral-ruleset-validator.ts
Signature: toArray<T>(value: T | T[] | undefined): T[]
Description: Normalizes a value to an array. Returns an empty array for undefined, wraps a non-array value in a single-element array, and returns arrays unchanged.

Type: Function
Name: validateSpectralRuleset
Location: packages/insomnia/src/common/spectral-ruleset-validator.ts
Signature: validateSpectralRuleset(content: string): { isValid: true } | { isValid: false; error: string }
Description: Validates the content of a Spectral ruleset file. Returns { isValid: true } for valid content. Returns { isValid: false, error: string } for invalid content, where the error message matches specific patterns described in requirements. Both functions are exported from this module.

Type: Function
Name: bundleSpectralRuleset
Location: packages/insomnia/src/common/bundle-spectral-ruleset.ts
Signature: bundleSpectralRuleset(filePath: string): Promise<string>
Description: Reads a Spectral ruleset YAML file from the given path and returns a bundled YAML string with all local file extends flattened (child rules merged into parent, with parent rules taking precedence on name collision). Built-in spectral identifiers (spectral:oas, spectral:asyncapi, spectral:arazzo) are preserved in the output. Remote HTTPS extends are validated by fetching and running validateSpectralRuleset, then preserved as URLs in the output (remote content is NOT merged). Uses fs.promises.readFile to read local files and the global fetch for remote URLs.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.