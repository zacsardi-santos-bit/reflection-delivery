Implement a public field in the `CronExpression` struct to expose the parsed cron schedule for human-readable descriptions. Ensure the `CronExpression` can be constructed from shorthand cron expressions and standard formats.

Requirements:

*   Update `CronExpression` struct in `lib/config/src/app/job.rs`:
    *   Add a public field `cron` of type `saffron::parse::CronExpr`.
    *   Add a public field `parsed_from` of type `String` to store the original input string.
*   Implement `FromStr` for `CronExpression`:
    *   Ensure parsing of shorthand aliases:
        *   "@hourly" maps to "0 * * * *"
        *   "@daily" maps to "0 0 * * *"
        *   "@weekly" maps to "0 0 * * 1"
        *   "@monthly" maps to "0 0 1 * *"
        *   "@yearly" maps to "0 0 1 1 *"
    *   Support arbitrary valid saffron cron expression strings.
*   Ensure that parsing "@hourly" and calling `.cron.describe(saffron::parse::English::default()).to_string()` returns "Every hour".
*   Modify `lib/config/Cargo.toml`:
    *   Add `saffron = { version = "0.1.0", features = ["std"] }` as a dependency.
    *   Remove the previous `cron` crate dependency.
*   Update `JobTrigger` enum to use `CronExpression` for its `Cron` variant instead of the old `CronSchedule` enum.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.