I'm working with as-of joins in polars and running into two issues with the "nearest" matching strategy.

First, there's no way to set a tolerance when using the nearest strategy. With the backward and forward strategies I can specify a maximum distance beyond which a match shouldn't be made — any left row with no right row within tolerance gets a null result. But when I switch to nearest, there's no equivalent behavior: it always finds the globally nearest value even if it's very far away. I'd like to be able to pass a numeric distance (or a duration for date/time columns) as a tolerance for nearest joins, just like I can for other strategies. For date/time columns, it would also be convenient to pass a time-interval object directly as the tolerance, rather than having to write it as a string.

Second, I've noticed a correctness bug: when the last row in the right-hand DataFrame is the nearest match for several consecutive rows in the left-hand DataFrame, only some of those left rows end up correctly matched. The others get wrong or missing results. This seems to be a state issue where some internal distance value isn't being reset between left rows. Both the plain nearest join and the grouped variant seem to be affected.

Can both of these be fixed? The tolerance feature should also work when the join is grouped by a column.
