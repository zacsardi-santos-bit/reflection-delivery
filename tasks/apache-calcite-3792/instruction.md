Implement a comprehensive timestamp-to-string formatter for Apache Calcite's PostgreSQL-compatible SQL dialect. This formatter should support a wide range of PostgreSQL-style format patterns and modifiers, ensuring compatibility with PostgreSQL's rich date/time formatting capabilities.

*   Create the `PostgresqlDateTimeFormatter` class in `core/src/main/java/org/apache/calcite/util/format/PostgresqlDateTimeFormatter.java`.
    *   Implement a static method `toChar(String pattern, ZonedDateTime dateTime)` that formats a `ZonedDateTime` according to a PostgreSQL-style format pattern.
*   Support the following format pattern tokens:
    *   Time patterns: `HH`, `HH12`, `HH24`, `MI`, `SS`, `SSSS`, `SSSSS`, `MS`, `US`, `FF1`, `FF2`, `FF3`, `FF4`, `FF5`, `FF6`.
    *   AM/PM patterns: `AM`, `PM`, `am`, `pm`, `A.M.`, `P.M.`, `a.m.`, `p.m.`.
    *   Year patterns: `Y,YYY`, `YYYY`, `YYY`, `YY`, `Y`.
    *   ISO week-year patterns: `IYYY`, `IYY`, `IY`, `I`.
    *   Era patterns: `AD`, `BC`, `ad`, `bc`, `A.D.`, `B.C.`, `a.d.`, `b.c.`.
    *   Month patterns: `MONTH`, `Month`, `month`, `TMMONTH`, `MON`, `Mon`, `mon`, `MM`.
    *   Day-of-week name patterns: `DAY`, `Day`, `day`, `TMDAY`, `DY`, `Dy`, `dy`.
    *   Day number patterns: `DDD`, `IDDD`, `DD`, `D`, `ID`.
    *   Week/century/other patterns: `W`, `WW`, `IW`, `CC`, `J`, `Q`, `RM`, `rm`.
*   Implement modifier behaviors:
    *   `FM` prefix: Removes leading zeros from numeric outputs.
    *   `TH`/`th` suffix: Appends English ordinal suffixes to numeric values.
*   Ensure the formatter returns `null` when either the timestamp or format argument is `null`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.