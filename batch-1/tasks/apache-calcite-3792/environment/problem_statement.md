## Description

Apache Calcite's PostgreSQL-compatible dialect includes a timestamp formatting function, but it only supports a very limited set of format codes. Developers writing SQL queries that rely on PostgreSQL's rich date/time formatting — such as 12-hour clock hours, sub-second precision at various levels (tenths, hundredths, microseconds), AM/PM indicators (with and without dots), era designators, ISO week-year values, ordinal suffixes, fill-mode (leading-zero removal), Roman numeral months, and locale-translated day/month names — either get wrong results or cannot use those patterns at all.

## Expected Behavior

- Formatting tokens for the 12-hour clock, 24-hour clock, minutes, seconds, and sub-second precision at 1 through 6 digits must all be supported.
- AM/PM indicators should be available in uppercase, lowercase, and dot-separated variants.
- Year formatting should support 1-, 2-, 3-, and 4-digit variants of both calendar year and ISO week year, as well as the comma-separated four-digit form.
- Era indicators (before/after common era) should be available in multiple capitalisation styles, with and without dots.
- Full and abbreviated month names and day-of-week names should support uppercase, title-case, and lowercase variants. By default the names should be in English; a translation modifier should enable output in the JVM default locale.
- Full day-of-week names should be padded with trailing spaces to a consistent width.
- Numeric day-of-year, day-of-month, day-of-week (both standard and ISO), week-of-month, week-of-year, ISO week, century, Julian day, and quarter tokens must all be supported.
- Month in Roman numerals (uppercase and lowercase) must be supported.
- A fill-mode prefix should suppress leading zeros on numeric values.
- Uppercase and lowercase ordinal suffix modifiers should append the correct English ordinal to any numeric value.
- When either the timestamp or format argument is null, the result should be null.

## Why This Matters

Without these format codes, SQL queries that work in real PostgreSQL cannot be executed correctly through Calcite's PostgreSQL dialect. This gap makes it difficult to migrate or integrate applications that rely on PostgreSQL's standard date/time formatting capabilities.
