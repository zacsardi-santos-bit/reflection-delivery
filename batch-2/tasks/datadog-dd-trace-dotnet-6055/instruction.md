Add the host's logical CPU count as a read-only metric to CI test session spans in the `dd-trace-dotnet` project. Ensure this metric is captured automatically at session start and cannot be overridden by external code. Update relevant classes and generated files to support serialization and enumeration of this new metric.

*   Add a constant to the `CommonTags` class:
    *   File: `tracer/src/Datadog.Trace/Ci/Tags/CommonTags.cs`
    *   Add: `public const string LogicalCpuCount = "_dd.host.vcpu_count";`

*   Update the `TestSessionSpanTags` class:
    *   File: `tracer/src/Datadog.Trace/Ci/Tagging/TestSessionSpanTags.cs`
    *   Add a read-only property: `[Metric(CommonTags.LogicalCpuCount)] public double? LogicalCpuCount { get; }`
    *   Initialize `LogicalCpuCount` in the constructor with `Environment.ProcessorCount`.
    *   Ensure `SetMetric` ignores the key `"_dd.host.vcpu_count"`.

*   Modify generated partial class files for `TestSessionSpanTags`:
    *   Locations:
        *   `tracer/src/Datadog.Trace/Generated/net461/Datadog.Trace.SourceGenerators/TagListGenerator/TestSessionSpanTags.g.cs`
        *   `tracer/src/Datadog.Trace/Generated/net6.0/Datadog.Trace.SourceGenerators/TagListGenerator/TestSessionSpanTags.g.cs`
        *   `tracer/src/Datadog.Trace/Generated/netcoreapp3.1/Datadog.Trace.SourceGenerators/TagListGenerator/TestSessionSpanTags.g.cs`
        *   `tracer/src/Datadog.Trace/Generated/netstandard2.0/Datadog.Trace.SourceGenerators/TagListGenerator/TestSessionSpanTags.g.cs`
    *   Add a static `ReadOnlySpan<byte>` field: 
        *   `private static ReadOnlySpan<byte> LogicalCpuCountBytes => new byte[] { 179, 95, 100, 100, 46, 104, 111, 115, 116, 46, 118, 99, 112, 117, 95, 99, 111, 117, 110, 116 };`
    *   Override `GetMetric(string key)` to return `LogicalCpuCount` for key `"_dd.host.vcpu_count"`.
    *   Override `SetMetric(string key, double? value)` to ignore key `"_dd.host.vcpu_count"`.
    *   Override `EnumerateMetrics<TProcessor>(ref TProcessor processor)` to process `LogicalCpuCount` when not null.
    *   Override `WriteAdditionalMetrics(System.Text.StringBuilder sb)` to append `"_dd.host.vcpu_count (metric):"` and the value when `LogicalCpuCount` is not null.

*   Ensure CI test session spans include the metric `"_dd.host.vcpu_count"` with a value equal to `Environment.ProcessorCount`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.