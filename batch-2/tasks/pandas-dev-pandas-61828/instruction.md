I'm trying to add a Series to a DataFrame along a specific axis while filling in missing values with a default number, but it raises a "not implemented" error whenever I provide a fill value.

*   DataFrame.add, DataFrame.sub, DataFrame.mul (and other flex arithmetic methods) must accept a fill_value parameter when the other operand is a Series, instead of raising NotImplementedError. When fill_value is provided, NaN values in either the DataFrame or the Series are replaced with fill_value before the arithmetic operation is performed.

*   DataFrame.add(series, axis=0, fill_value=value): axis=0 aligns the Series index against the DataFrame index; NaN in either operand is replaced by fill_value. Example: df={'A':[1.0,nan],'B':[nan,4.0]}, ser=[nan,2.0], fill_value=10, axis=0 → {'A':[11.0,12.0],'B':[nan,6.0]}.

*   DataFrame.add(series, axis=1, fill_value=value): axis=1 aligns the Series index against the DataFrame columns; NaN in either operand is replaced by fill_value. Example: df={'A':[1.0,nan],'B':[nan,4.0]}, ser=[nan,2.0] indexed to ['A','B'], fill_value=10, axis=1 → {'A':[11.0,nan],'B':[12.0,6.0]}.

*   DataFrame.add(series, axis=0|1, fill_value=value) with mismatched index/columns must expand the result to include all labels from both operands, applying fill_value where labels exist in only one operand.

*   DataFrame.mul(array, axis=0|1, fill_value=fill_val) must support pandas array operands (not just numpy arrays), including integer extension array dtypes and float extension array dtypes. NaN values in either operand are replaced by fill_val before multiplication.

*   DataFrame.sub with a zero-length DataFrame operand and fill_value must compute the result correctly rather than raising NotImplementedError. df_len0.sub(df, axis=None, fill_value=3) returns DataFrame([[2,1],[0,-1]]), df_len0.sub(df['A'], axis=0, fill_value=3) returns DataFrame([[2,2],[0,0]]), df_len0.sub(df['A'], axis=1, fill_value=3) returns empty DataFrame with columns ['A','B',0,1].

*   When a fill_value is supplied but the fill value type is incompatible with the operand types (e.g., string fill value on an integer Series), a TypeError must be raised (not NotImplementedError). The error message must match: 'unsupported operand type(s) for +: \'int\' and \'str\''.

*   Adding a PeriodArray and a DatetimeArray must raise TypeError with the message 'cannot add PeriodArray and DatetimeArray' consistently regardless of the container type (Index, Series, or DataFrame).

*   For boolean arrays, the __add__ and __radd__ operators must be excluded from the invalid array-like TypeError checks (alongside __mul__ and __rmul__).


*   Interface details: Type: Method
Name: add
Location: pandas/core/frame.py (DataFrame class)
Signature: add(other, axis='columns', level=None, fill_value=None) -> DataFrame
Description: Flexible addition for DataFrame. When `other` is a Series and `fill_value` is provided, the fill_value must be used to replace NaN values in both the DataFrame and the Series before the operation is performed. Previously raised NotImplementedError in this case. Supports both axis=0 (align Series index with DataFrame index) and axis=1 (align Series index with DataFrame columns). Works with both aligned and mismatched indices.

Type: Method
Name: sub
Location: pandas/core/frame.py (DataFrame class)
Signature: sub(other, axis='columns', level=None, fill_value=None) -> DataFrame
Description: Flexible subtraction for DataFrame. When `other` is a Series and `fill_value` is provided, the fill_value must be applied to NaN in both operands before the operation. Also supports zero-length DataFrame as self with a fill_value, computing the correct result rather than raising NotImplementedError.

Type: Method
Name: mul
Location: pandas/core/frame.py (DataFrame class)
Signature: mul(other, axis='columns', level=None, fill_value=None) -> DataFrame
Description: Flexible multiplication for DataFrame. When `other` is a pandas array (including extension array dtypes such as integer EA and float EA) and `fill_value` is provided, the fill_value must be applied to NaN values before multiplication. Supports both axis=0 and axis=1.

Type: Method (internal)
Name: _flex_arith_method
Location: pandas/core/frame.py (DataFrame class)
Signature: _flex_arith_method(self, other, op, *, axis: Axis = 'columns', level=None, fill_value=None) -> DataFrame
Description: Internal implementation of the flexible arithmetic operations. Must NOT raise NotImplementedError when `other` is a Series and `fill_value` is not None. The fill_value should be passed through to the underlying operation machinery.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.