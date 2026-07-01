I'm working with Daft dataframes and I often need to apply custom logic to each group of rows after a groupby operation. Right now I can only use the built-in aggregations like sum, count, and mean, but I need the ability to pass an arbitrary user-defined function to each group — one that receives all the rows for that group at once and can return any number of output rows.

For example, I'd like to group a dataframe by one or more keys, then apply a custom function that computes some statistic across all values in the group and returns the result. The output dataframe should contain the group key columns alongside the result column from the user-defined function. The result column's name should come from the first input expression I pass to the function.

When my function returns multiple output rows for a group, I'd expect the corresponding group key values to be repeated in the output to match the number of rows. This should work whether I'm grouping on a single column, multiple columns, or when there happens to be only one group in the data. I'd also like to be able to pass compound expressions (with aliases or arithmetic) as inputs to my function, not just plain column references.

Could you add a method to the grouped dataframe object that enables this pattern?
