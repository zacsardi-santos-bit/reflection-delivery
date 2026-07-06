I'm working with a dataframe that has several columns, and I'd like to use the top-k and bottom-k expression operations to select the k rows with the highest or lowest values in a specific column while also retrieving the corresponding values from other columns at the same time. Right now these operations seem to only work on a single column at a time without any way to specify which column drives the ranking.

I also need to be able to control the sort direction — both as a single flag for simple cases and as a per-column list when ranking by multiple columns. These operations should work inside grouped aggregations as well, so I can get the top or bottom k rows per group ranked by a chosen column.

When something invalid is passed — like a list of sort directions without any ranking column, or a list of directions that doesn't match the number of ranking columns — I'd expect a clear, descriptive error rather than a silent wrong result.
