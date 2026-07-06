I'd like to add support for installing packages from the GitHub Packages npm registry using a short prefix syntax, similar to how other package managers let you reference alternative registries.

*   The getOptionsFromPnpmSettings function must expand environment variable patterns (e.g., ${VAR}) found in the URL string values of the 'registries' object in its settings argument, returning an options object whose registries field contains the resolved URLs.

*   The getOptionsFromPnpmSettings function must expand environment variable patterns (e.g., ${VAR}) found in the URL string values of the 'namedRegistries' object in its settings argument, returning an options object whose namedRegistries field contains the resolved URLs.

*   The replaceVersionInBareSpecifier function must accept a third optional parameter namedRegistryPrefixes (readonly string[], default []) listing known named-registry prefix strings such as 'gh:' or 'work:'.

*   When replaceVersionInBareSpecifier is called with a specifier that starts with a prefix from namedRegistryPrefixes and the body after that prefix is a valid semver range, the function must return the prefix concatenated with just the version (e.g., 'gh:^1.0.0' + '1.1.0' → 'gh:1.1.0').

*   When replaceVersionInBareSpecifier is called with a specifier that starts with a named-registry prefix and the body contains a package name followed by a version (e.g., 'gh:@acme/foo@^1.0.0'), the function must replace the version part and return 'gh:@acme/foo@1.1.0'.

*   When replaceVersionInBareSpecifier is called with a specifier that starts with a named-registry prefix but no version segment (e.g., 'gh:@acme/foo'), the function must append the version and return 'gh:@acme/foo@1.1.0'.

*   When replaceVersionInBareSpecifier is called with specifiers using schemes not in namedRegistryPrefixes (workspace:, file:, link:, catalog:, github:, https: URLs, or any alias not present in the supplied array), the function must return the original specifier unchanged.

*   The parseBareSpecifier function must be updated so that when the specifier is 'npm:<value>' and the value is a valid semver range (e.g., 'npm:^1.0.0') and an alias is provided, it uses the alias as the package name rather than trying to parse one from the specifier body.

*   A new exported function parseNamedRegistrySpecifierToRegistryPackageSpec(rawSpecifier, knownRegistryNames, packageAlias, defaultTag) must be added to resolving/npm-resolver/src/parseBareSpecifier.ts. It must return null for specifiers that do not start with a known named-registry alias (including npm:, jsr:, catalog:, workspace: prefixes and the github: git shorthand). It must return null when the alias prefix is not in the knownRegistryNames set.

*   parseNamedRegistrySpecifierToRegistryPackageSpec must return null when the specifier is '<alias>:<version_selector>' and no packageAlias is provided, because the package name cannot be determined.

*   parseNamedRegistrySpecifierToRegistryPackageSpec must return a NamedRegistryPackageSpec object with fields { name: string, fetchSpec: string, type: 'tag'|'version'|'range', registryName: string } for valid specifiers. The name must be the original package name without any scope rewriting. When the specifier contains only a version selector with a packageAlias, name comes from packageAlias.

*   parseNamedRegistrySpecifierToRegistryPackageSpec must throw an error with code 'ERR_PNPM_INVALID_NAMED_REGISTRY_PACKAGE_NAME' when the specifier body starts with a scope character but has no valid package name (e.g., '@acme', '@acme/', '@acme@version'). The error message must include the alias with colon (e.g., "'work:'") so the user can identify the offending specifier.

*   A new exported interface NamedRegistryPackageSpec must be added to resolving/npm-resolver/src/parseBareSpecifier.ts. It extends RegistryPackageSpec with a registryName string field.

*   A new exported constant BUILTIN_NAMED_REGISTRIES must be added to resolving/npm-resolver/src/parseBareSpecifier.ts mapping the 'gh' alias to 'https://npm.pkg.github.com/'.

*   The createNpmResolver function's options type must accept an optional namedRegistries field (Record<string, string>). When namedRegistries contains a URL that is not a valid http or https URL (e.g., missing scheme, or using ftp:// scheme), createNpmResolver must throw synchronously with error code 'ERR_PNPM_INVALID_NAMED_REGISTRY_URL'.

*   createNpmResolver must return a resolveFromNamedRegistry function alongside the existing resolveFromNpm, resolveFromJsr, and clearCache. User-defined entries in namedRegistries must override built-in aliases (including the built-in 'gh' alias).

*   resolveFromNamedRegistry must return null for specifiers whose prefix is not a configured named-registry alias, for the 'github:' git shorthand scheme, and when no alias is provided for a bare version selector (e.g., 'gh:2.0.0' with no wantedDependency.alias).

*   resolveFromNamedRegistry must throw with error code 'ERR_PNPM_INVALID_NAMED_REGISTRY_PACKAGE_NAME' when the specifier names a scope without a valid package name (e.g., 'gh:@acme', 'gh:@acme@2.0.0').

*   resolveFromNamedRegistry must return a NamedRegistryResolveResult with fields: resolvedVia set to 'named-registry', registryName (the matched alias string), id ('<name>@<version>'), latest (latest dist-tag version), manifest (package manifest), resolution (object with integrity and tarball URL), alias (the resolved scoped package name).

*   When calcSpecifier is true, resolveFromNamedRegistry must populate normalizedBareSpecifier as '<alias>:<range>' when the dependency alias equals the package name, or '<alias>:<pkgName>@<range>' when the dependency alias differs from the package name.

*   resolveFromNamedRegistry must request the auth header by calling getAuthHeader with the named registry URL (not the default npm registry URL), so that per-URL credential entries in .npmrc take effect for named-registry specifiers.

*   resolveFromNamedRegistry must cache package metadata under the ABBREVIATED_META_DIR subdirectory of the configured cacheDir, using a path based on the named registry hostname and scoped package name.

*   A new exported interface NamedRegistryResolveResult must be added to resolving/npm-resolver/src/index.ts with fields: resolvedVia ('named-registry'), registryName (string), alias (string), manifest (DependencyManifest), resolution (TarballResolution).


*   Interface details: Type: Constant
Name: BUILTIN_NAMED_REGISTRIES
Location: resolving/npm-resolver/src/parseBareSpecifier.ts
Description: A frozen record mapping the built-in named-registry alias 'gh' to its default registry URL 'https://npm.pkg.github.com/'. Exported so other packages can merge user-defined aliases on top of it.
Signature: Readonly<Record<string, string>> = { gh: 'https://npm.pkg.github.com/' }

Type: Interface
Name: NamedRegistryPackageSpec
Location: resolving/npm-resolver/src/parseBareSpecifier.ts
Description: Extends RegistryPackageSpec with a registryName field identifying which named-registry alias was matched. Fields: name (string), fetchSpec (string), type ('tag' | 'version' | 'range'), registryName (string).

Type: Function
Name: parseNamedRegistrySpecifierToRegistryPackageSpec
Location: resolving/npm-resolver/src/parseBareSpecifier.ts
Signature: parseNamedRegistrySpecifierToRegistryPackageSpec(rawSpecifier: string, knownRegistryNames: ReadonlySet<string>, packageAlias: string | undefined, defaultTag: string) -> NamedRegistryPackageSpec | null
Description: Parses a named-registry specifier of the form '<alias>:<body>' into a NamedRegistryPackageSpec. Returns null when the specifier does not use a recognised alias, or when the alias is not in knownRegistryNames, or when the specifier is '<alias>:<version_selector>' and no packageAlias is provided. Throws PnpmError with code 'ERR_PNPM_INVALID_NAMED_REGISTRY_PACKAGE_NAME' when the body names a scope without a valid package path (e.g. '@acme', '@acme/', '@acme@version'). The error message must include the alias with its colon (e.g. "'work:'"). Must return null for the 'github:' git shorthand — that scheme belongs to the git resolver. Package names are returned as-is, without scope rewriting.

Type: Function
Name: parseBareSpecifier (updated)
Location: resolving/npm-resolver/src/parseBareSpecifier.ts
Signature: parseBareSpecifier(bareSpecifier: string, alias: string | undefined, defaultTag: string, registry: string) -> RegistryPackageSpec | null
Description: Existing function — updated so that 'npm:<semver_range>' combined with a non-undefined alias uses the alias as the package name (mirrors the named-registry shape). For example, parseBareSpecifier('npm:^1.0.0', 'is-positive', 'latest', registry) must return { name: 'is-positive', type: 'range', ... }.

Type: Function
Name: replaceVersionInBareSpecifier (updated)
Location: installing/deps-resolver/src/replaceVersionInBareSpecifier.ts
Signature: replaceVersionInBareSpecifier(bareSpecifier: string, version: string, namedRegistryPrefixes?: readonly string[]) -> string
Description: Existing function — gains a third optional parameter namedRegistryPrefixes (defaults to []). For a specifier whose prefix (including colon) appears in namedRegistryPrefixes: if the body after the prefix is a valid semver range, replaces the entire body with the version ('gh:^1.0.0' → 'gh:1.1.0'); otherwise replaces the @<version> tail or appends it. Specifiers with schemes not in namedRegistryPrefixes (workspace:, file:, link:, catalog:, github:, https: URLs, or any alias absent from the array) must be returned unchanged.

Type: Interface
Name: NamedRegistryResolveResult
Location: resolving/npm-resolver/src/index.ts
Description: Resolve result returned by resolveFromNamedRegistry. Fields: resolvedVia ('named-registry'), registryName (string — the matched alias), alias (string — the resolved scoped package name), manifest (DependencyManifest), resolution (TarballResolution with integrity and tarball URL), id (PkgResolutionId), latest (string, optional), normalizedBareSpecifier (string, optional).

Type: Function
Name: createNpmResolver (updated)
Location: resolving/npm-resolver/src/index.ts
Signature: createNpmResolver(fetchFromRegistry: FetchFromRegistry, getAuthHeader: GetAuthHeader, opts: ResolverFactoryOptions) -> { resolveFromNpm: NpmResolver, resolveFromJsr: NpmResolver, resolveFromNamedRegistry: NpmResolver, clearCache: () => void }
Description: Existing factory function — updated to accept an optional namedRegistries field (Record<string, string>) in opts. User-defined entries override built-in aliases (including the built-in 'gh' alias). Must throw synchronously with PnpmError code 'ERR_PNPM_INVALID_NAMED_REGISTRY_URL' when any value in namedRegistries is not a valid http or https URL (e.g. missing scheme, ftp:// scheme). Now returns resolveFromNamedRegistry in addition to the existing fields.

Type: Function
Name: resolveFromNamedRegistry
Location: resolving/npm-resolver/src/index.ts
Signature: resolveFromNamedRegistry(wantedDependency: WantedDependency & { optional?: boolean }, opts: Omit<ResolveFromNpmOptions, 'registry'>) -> Promise<NamedRegistryResolveResult | null>
Description: Resolves a '<alias>:<body>' specifier against the configured named registry. Returns null for unrecognised prefixes, the 'github:' git shorthand, or when no wantedDependency.alias is provided for a bare version selector. Throws PnpmError with code 'ERR_PNPM_INVALID_NAMED_REGISTRY_PACKAGE_NAME' for scope-only names. Auth header is obtained by calling getAuthHeader with the named registry URL (not the default npm registry). Metadata is cached in ABBREVIATED_META_DIR under cacheDir. When opts.calcSpecifier is true, normalizedBareSpecifier is '<alias>:<range>' when the dependency alias equals the package name, or '<alias>:<pkgName>@<range>' otherwise. The alias field of the result is the resolved scoped package name. resolvedVia must be 'named-registry'.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.