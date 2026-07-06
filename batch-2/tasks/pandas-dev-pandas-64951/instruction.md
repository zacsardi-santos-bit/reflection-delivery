I'm running into a bug with set operations on unordered categorical indexes in pandas.

*   When calling union on an unordered CategoricalIndex whose categories are the same set as another unordered CategoricalIndex but listed in a different order, the result must be a CategoricalIndex containing values from the first index first, followed by values from the second index not present in the first, using the categories of the first index.

*   When calling intersection on an unordered CategoricalIndex whose categories are the same set as another unordered CategoricalIndex but listed in a different order, the result must be a CategoricalIndex containing only elements present in both indexes, using the categories of the first index.

*   When the two unordered CategoricalIndex objects share no common elements, the intersection must return an empty CategoricalIndex whose categories match those of the first index (not the second).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.