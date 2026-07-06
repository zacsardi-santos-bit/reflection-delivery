## Description

After merging parts in a table with a low-cardinality string column, queries can silently return wrong values when the merge produces multiple separate dictionaries within a single granule mark interval.

## Background

Low-cardinality columns in ClickHouse store their data efficiently using a dictionary. When a merge produces output blocks that each overflow the dictionary size limit, each block writes its own new dictionary. If the entire merged part is covered by a single large index mark (because the granule size is larger than the total number of rows), all the per-block dictionary entries end up at the same mark position in the index.

## The Bug

The reader uses mark positions as a heuristic to decide whether a part has a single shared dictionary: if all the dictionary marks point to the same file offset, the part is treated as a single-dictionary part and the first dictionary is cached and reused for all reads. However, when multiple dictionaries are written within that single mark interval, this heuristic is wrong. The reader picks up only the first dictionary and uses it for all subsequent row lookups — rows whose string values only appear in later dictionaries are decoded incorrectly.

## Expected Behavior

- Reading rows that span multiple dictionary blocks within a single granule must return correct string values for all rows, regardless of which dictionary block their value was originally written in.
- The single-dictionary optimization should only apply when the part genuinely contains one dictionary covering all rows, not when the marks appear uniform but multiple dictionaries were actually written.

## Why This Matters

This is a data-correctness bug. Users running queries after merges on tables with many distinct low-cardinality string values may receive silently wrong results — there is no error, just incorrect data returned.
