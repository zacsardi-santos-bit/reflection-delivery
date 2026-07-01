Implement the `cast_sf_timestamp_to_arrow_timestamp` function to handle Snowflake timestamp columns returned as plain integers or structured types. Ensure the function converts epoch-second values to nanoseconds and handles both timezone-aware and non-timezone-aware modes correctly.

*   Update the `cast_sf_timestamp_to_arrow_timestamp` function located in `crates/db_connection_pool/src/dbconnection/snowflakeconn.rs` with the following requirements:
    *   Accept an input array that can be either a plain 64-bit integer array or a structured array with epoch and fractional fields.
    *   For a 64-bit integer array input:
        *   Convert each integer value representing epoch seconds to nanoseconds by multiplying by 1,000,000,000.
        *   Ensure the conversion works for both timezone-aware (`is_tz=true`) and non-timezone-aware (`is_tz=false`) modes.
            *   In timezone-aware mode, the resulting timestamp array must include UTC timezone metadata.
    *   For a structured array input:
        *   Continue using the existing logic to convert epoch and fractional fields to nanoseconds.
    *   If the input array is neither a structured array nor a plain 64-bit integer array, return an error indicating the input type is unsupported.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.