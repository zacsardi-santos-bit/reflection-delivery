Enhance the Trivy Gradle lockfile analyzer to mark all discovered packages as indirect dependencies, scan entire project directories for lockfiles, and enrich package data with license and dependency information from the Gradle artifact cache when available.

*   Update the Gradle lockfile parser:
    *   Set `Indirect` to `true` on all `types.Library` objects produced.
*   Implement the `newGradleLockAnalyzer(opts analyzer.AnalyzerOptions) (analyzer.PostAnalyzer, error)` function:
    *   Return an initialized analyzer instance implementing `PostAnalyze`.
*   Implement the `PostAnalyze` method:
    *   Signature: `PostAnalyze(ctx context.Context, input analyzer.PostAnalysisInput) (*analyzer.AnalysisResult, error)`.
    *   Accept a filesystem (`fs.FS`) in `PostAnalysisInput.FS` representing a project directory.
    *   Search for `gradle.lockfile` files and parse each one.
    *   Ensure `FilePath` in the resulting `Application` is the relative path.
    *   Set `Indirect` to `true` for all returned packages.
    *   If `GRADLE_USER_HOME` is set, enrich packages with:
        *   Declared licenses as `[]string`.
        *   Direct dependencies as `DependsOn []string` in 'groupID:artifactID:version' format.
    *   If `GRADLE_USER_HOME` is not set or `caches` subdirectory does not exist, return packages without enrichment.
*   Implement `parsePom(r io.Reader, path string) (pomXML, error)` function:
    *   Parse a Maven POM XML file and return a `pomXML` struct.
    *   Extract `GroupId` and `Version` from the path if missing in XML.
    *   Resolve property-referenced dependency versions using the `Properties` map.
    *   Return a zero-value `pomXML` struct if no licenses or dependencies are present.
*   Define the `pomXML` struct with fields:
    *   `GroupId`, `ArtifactId`, `Version`, `Properties`, `Dependencies`, `Licenses`.
*   Define supporting structs:
    *   `Licenses` with `License []License`.
    *   `License` with `Name string`.
    *   `Dependencies` with `Dependency []Dependency`.
    *   `Dependency` with `GroupID`, `ArtifactID`, `Version` string fields.
*   Define `Properties` as a map type for key-value pairs.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.