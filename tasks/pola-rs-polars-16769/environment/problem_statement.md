## Description

The deprecation period for implicit key-column coalescing in left joins is complete. The new default behavior for left joins should be to **not** coalesce key columns — both the left and right join key columns should appear in the output, just as they do in inner and other join types. Users who want the old single-key-column output must explicitly opt in.

Previously, when you performed a left join in Polars, the join key columns from both sides were automatically merged into one column. The right side's key column was silently dropped. This was inconsistent with other join types and prevented users from distinguishing which key values came from which side. A deprecation warning was introduced to give users time to adapt, and now that period is over.

## Expected Behavior

- A left join performed without explicitly requesting column coalescing should include both the left and right key columns in the output. The right-side key column should appear with a disambiguating suffix appended to its name when it shares a name with the left key column.
- A left join performed with coalescing explicitly enabled should produce the old behavior: one merged key column, no right-side key in the output.
- No deprecation warning should be emitted when performing a left join without specifying a coalesce preference — the new non-coalescing default is now standard.
- Output shapes from left joins (without explicit coalescing) should reflect the additional right-side key column(s).
- This behavior applies consistently across streaming and non-streaming execution modes.

## Why This Matters

Users were confused by left joins silently dropping the right-side key column, making it hard to verify which values were actually matched. The new default makes left join output predictable and consistent with the rest of the join API.
