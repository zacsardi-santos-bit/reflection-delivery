I'm working on adding support for a new standardized Python lock file format to Trivy.

*   The Parse function in the pylock dependency parser must accept a context.Context as its first parameter (before the reader), following the standard parser interface. The context parameter may be unused.

*   The Project struct in the pyproject parser must include an OptionalDependencies field of type map[string]Dependencies with TOML tag 'optional-dependencies', so that optional dependency groups (extras) defined in pyproject.toml are parsed and accessible.

*   A new pylockAnalyzer struct must be created in the package pkg/fanal/analyzer/language/python/pylock. The struct must have a Required(filePath string, fi os.FileInfo) bool method and a PostAnalyze(ctx context.Context, input analyzer.PostAnalysisInput) (*analyzer.AnalysisResult, error) method.

*   The Required method must return true for: 'pylock.toml' (exact match), 'pylock.<identifier>.toml' where identifier contains no dots and is non-empty (e.g. 'pylock.linux.toml', 'pylock.prod.toml'), 'pyproject.toml', and any of these filenames nested under subdirectories. It must return false for filenames with multiple dots in the identifier (e.g. 'pylock.linux.arm64.toml'), empty identifiers (e.g. 'pylock..toml'), unrelated filenames (e.g. 'requirements.txt'), and files that do not start with 'pylock' (e.g. 'notpylock.toml').

*   A newPylockAnalyzer(opts analyzer.AnalyzerOptions) (analyzer.PostAnalyzer, error) constructor function must exist and must be registered as the post-analyzer for the TypePyLock analyzer type via analyzer.RegisterPostAnalyzer.

*   PostAnalyze must walk the provided filesystem, parse pylock.toml (and optionally pyproject.toml) files, and return an *analyzer.AnalysisResult containing types.Application entries with Type set to types.PyLock.

*   When pyproject.toml is present alongside pylock.toml, packages listed in project.dependencies and project.optional-dependencies must be marked with RelationshipDirect. All other non-root packages must be marked with RelationshipIndirect. The root package (the project itself, if present) must be marked with RelationshipRoot and have its DependsOn field populated with the IDs of direct dependencies.

*   When PostAnalyze encounters a broken/unparseable pylock.toml, it must return an empty *analyzer.AnalysisResult (no Applications) and no error.

*   A new LangType constant named PyLock with string value 'pylock' must be added to pkg/fanal/types/const.go. The existing filename constant previously named PyLock must be renamed to PyLockFile (retaining the value 'pylock.toml') to avoid naming collision.

*   A new analyzer Type constant named TypePyLock with value 'pylock' must be added to pkg/fanal/analyzer/const.go and included in the relevant analyzer type lists (e.g. post-analyzers, language analyzers).

*   The PyLock type must be wired into the vulnerability detection ecosystem as a Pip/PyPI ecosystem type, so that vulnerability matching uses PEP 440 version comparison and PyPI PURL type.


*   Interface details: Type: Struct
Name: pylockAnalyzer
Location: pkg/fanal/analyzer/language/python/pylock/pylock.go
Description: Analyzer for pylock.toml files. Internal (unexported) struct. Must be directly instantiable with a zero-value struct literal (pylockAnalyzer{}) for use in the Required method test.
Signature: Required(filePath string, fi os.FileInfo) bool
Signature: PostAnalyze(ctx context.Context, input analyzer.PostAnalysisInput) (*analyzer.AnalysisResult, error)

Type: Function
Name: newPylockAnalyzer
Location: pkg/fanal/analyzer/language/python/pylock/pylock.go
Signature: newPylockAnalyzer(opts analyzer.AnalyzerOptions) (analyzer.PostAnalyzer, error)
Description: Constructor for pylockAnalyzer. Registered as a post-analyzer for the TypePyLock analyzer type. Returns an analyzer.PostAnalyzer interface.

Type: Function
Name: Parse
Location: pkg/dependency/parser/python/pylock/parse.go
Signature: Parse(_ context.Context, r xio.ReadSeekerAt) ([]ftypes.Package, []ftypes.Dependency, error)
Description: Updated signature — accepts context.Context as the first parameter before the reader. The context parameter is unused (blank identifier).

Type: Struct field
Name: OptionalDependencies
Location: pkg/dependency/parser/python/pyproject/pyproject.go
Description: New field on the Project struct. Type: map[string]Dependencies. TOML tag: "optional-dependencies". Maps extra/group name to the set of dependency names in that group.

Type: Constant
Name: PyLock
Location: pkg/fanal/types/const.go
Description: LangType constant with string value "pylock". Represents the pylock language/ecosystem type. Distinct from the filename constant (which should be named PyLockFile = "pylock.toml").

Type: Constant
Name: PyLockFile
Location: pkg/fanal/types/const.go
Description: Filename constant with string value "pylock.toml". Renamed from the previously existing PyLock filename constant to avoid collision with the new PyLock LangType constant.

Type: Constant
Name: TypePyLock
Location: pkg/fanal/analyzer/const.go
Description: Analyzer Type constant with value "pylock". Used to register and look up the pylockAnalyzer via analyzer.RegisterPostAnalyzer.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.