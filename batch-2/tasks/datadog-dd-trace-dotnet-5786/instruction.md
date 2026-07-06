I'm working on the MySQL integration tests for our.

*   MySQL version '9.0.0' must be included in the MySqlData package version lists (PackageVersionsLatestMajors.g.cs, PackageVersionsLatestMinors.g.cs, PackageVersionsLatestSpecific.g.cs) for each supported .NET target framework: NET462, NETCOREAPP3_1, NET5_0, NET6_0, NET7_0, and NET8_0.

*   The test data provider method GetMySqlData(bool newVersionsOnly) in MySqlCommandTests must replace the two separate methods GetMySql8Data() and GetOldMySqlData(). The new method must classify a version as 'new' when the version string is empty OR when Version.Parse(version).Major >= 8; all other versions are classified as 'old'.

*   When newVersionsOnly is true, GetMySqlData must yield only versions classified as 'new' (major >= 8 or empty string). When newVersionsOnly is false, it must yield only versions classified as 'old' (major < 8). Each version must be yielded once per schema version ('v0', 'v1') per propagation value (empty string, '100', 'randomValue', 'disabled', 'service', 'full').

*   The SubmitsTracesInMySql8 test must use GetMySqlData with parameters: true, and SubmitsTracesInOldMySql must use GetMySqlData with parameters: false.

*   In the MySQL sample application (Samples.MySql/Program.cs), the condition detecting whether the server is 'old' must use Major < 8, so MySQL version 9 and higher are treated as current (non-legacy) servers.

*   The MySQL sample application project (Samples.MySql.csproj) must reference System.Security.Permissions, System.Configuration.ConfigurationManager, System.Management, and System.Text.Encoding.CodePages all at version '8.0.0' (not '5.0.0').

*   The SQLite package version entry '6.0.31' must be updated to '6.0.32' in all three generated package version files (PackageVersionsLatestMajors.g.cs, PackageVersionsLatestMinors.g.cs, PackageVersionsLatestSpecific.g.cs) across all supported .NET target frameworks.


*   Interface details: Type: Method
Name: GetMySqlData
Location: tracer/test/Datadog.Trace.ClrProfiler.IntegrationTests/AdoNet/MySqlCommandTests.cs
Signature: public static IEnumerable<object[]> GetMySqlData(bool newVersionsOnly)
Description: Replaces the two separate methods GetMySql8Data() and GetOldMySqlData(). Iterates over PackageVersions.MySqlData and classifies each version as "new" when the version string is empty OR when Version.Parse(version).Major >= 8. When newVersionsOnly is true, yields only new versions; when false, yields only old versions. For each qualifying version, yields one row per combination of schema version ("v0", "v1") and propagation value (empty string, "100", "randomValue", "disabled", "service", "full") as object arrays: [version, schemaVersion, propagation].


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.