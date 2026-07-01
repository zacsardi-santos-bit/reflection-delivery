I've run into a bug with rolling window operations when using the grouping feature. If I sort my DataFrame by the grouping column before applying a rolling window grouped by that same column, the resulting group key column ends up with wrong values — the groups get misassigned in the output. No error is raised, the computation just silently returns incorrect group associations.

For example, I have a small DataFrame with two rows, each belonging to a different group and having a distinct timestamp. When I sort by the group identifier and then apply a rolling window partitioned by that group identifier, the group ids in the result are not what I'd expect — they don't correctly match back to the original groups.

It seems like empty sub-windows (windows that land inside a group but contain no matching rows) are somehow causing the group key offset calculation to go wrong. The fix should ensure that even when a time window within a group is empty, the group key for that window still points to the correct group rather than being offset incorrectly.

Could you fix the rolling window implementation so that grouping works correctly regardless of whether the input is sorted by time or by the group column?
