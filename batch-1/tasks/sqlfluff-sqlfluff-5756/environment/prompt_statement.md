I'm working with Snowflake SQL and trying to use SQLFluff to lint our queries, but it's failing to parse any query that uses Snowflake's time-series join feature. This is a special join type that matches rows based on the closest preceding timestamp, using a dedicated time-matching condition clause specified in parentheses. SQLFluff just throws a parse error and can't process the file at all.

The join syntax involves the join type keyword followed by a condition clause that takes a comparison expression in parentheses. Optionally there can also be an equality condition afterwards. These joins can appear multiple times in the same query, and can be combined with regular join types. The time-matching condition supports various comparison operators like greater than or equal, greater than, and less than.

I need SQLFluff to properly support this Snowflake join syntax — both parsing it cleanly and being able to lint/fix files containing it.
