Implement temporality configuration support for the OpenTelemetry Go metric SDK's reader types. Ensure that both manual and periodic readers can be configured to report metrics as cumulative totals or delta values based on a temporality selector function provided at construction time.

*   Update the Reader interface in `sdk/metric/reader.go`:
    *   Include a package-private method `temporality(kind InstrumentKind) Temporality`.
    *   Ensure all Reader implementations satisfy this method.

*   Modify `NewManualReader` in `sdk/metric/manual_reader.go`:
    *   Accept zero or more `ManualReaderOption` values as variadic arguments.
    *   Implement the package-private method `temporality(kind InstrumentKind) Temporality`.
    *   Default to `CumulativeTemporality` when no options are provided.
    *   Use the selector function from `WithTemporality(selector)` if provided, with the last option taking precedence.

*   Modify `NewPeriodicReader` in `sdk/metric/periodic_reader.go`:
    *   Accept `WithTemporality(selector)` as a `PeriodicReaderOption`.
    *   Implement the package-private method `temporality(kind InstrumentKind) Temporality`.
    *   Follow the same default and override behavior as `ManualReader`.

*   Implement `WithTemporality` in `sdk/metric/reader.go`:
    *   Accept a selector function of type `func(instrument InstrumentKind) Temporality`.
    *   Return a `ReaderOption` satisfying both `ManualReaderOption` and `PeriodicReaderOption`.
    *   Ensure the last `WithTemporality` option applied takes effect.

*   Define the `Temporality` type in `sdk/metric/temporality.go`:
    *   Use `uint8` with constants: `undefinedTemporality`, `CumulativeTemporality` (1), and `DeltaTemporality` (2).

*   Ensure `InstrumentKind` in `sdk/metric/instrumentkind.go`:
    *   Use `uint8` with a package-private zero-value constant `undefinedInstrument`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.