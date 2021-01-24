# float()

## 목차

* 파이썬 공식 문서 - 한글(번역)
    
* 파이썬 공식 문서 - 영어(원문)

## 파이썬 공식 문서 - 한글(번역)

주어진 인자를 리스트 인스턴스로 반환합니다.

## 파이썬 공식 문서 - 영어(원문)

class float([x])

Return a floating point number constructed from a number or string x.

If the argument is a string, it should contain a decimal number, optionally preceded by a sign, and optionally embedded in whitespace.<br>
The optional sign may be `'+'` or `'-'`; a `'+'` sign has no effect on the value produced.<br>
The argument may also be a string representing a NaN (not-a-number), or a positive or negative infinity.<br>
More precisely, the input must conform to the following grammar after leading and trailing whitespace characters are removed:

```
sign           ::=  "+" | "-"
infinity       ::=  "Infinity" | "inf"
nan            ::=  "nan"
numeric_value  ::=  floatnumber | infinity | nan
numeric_string ::=  [sign] numeric_value
```

Here `floatnumber` is the form of a Python floating-point literal, described in [Floating point literals](https://docs.python.org/3/reference/lexical_analysis.html#floating).<br>
Case is not significant, so, for example, "inf", "Inf", "INFINITY" and "iNfINity" are all acceptable spellings for positive infinity.

Otherwise, if the argument is an integer or a floating point number, a floating point number with the same value (within Python’s floating point precision) is returned.<br>
If the argument is outside the range of a Python float, an [OverflowError](https://docs.python.org/3/library/exceptions.html#OverflowError) will be raised.

For a general Python object x, `float(x)` delegates to `x.\_\_float\_\_()`. If `\_\_float\_\_()` is not defined then it falls back to [\_\_index\_\_()](https://docs.python.org/3/reference/datamodel.html#object.__index__).

If no argument is given, `0.0` is returned.

Examples:

```python
>>>
>>> float('+1.23')
1.23
>>> float('   -12345\n')
-12345.0
>>> float('1e-003')
0.001
>>> float('+1E6')
1000000.0
>>> float('-Infinity')
-inf
```

The float type is described in [Numeric Types -- int, float, complex](https://docs.python.org/3/library/stdtypes.html#typesnumeric).

Changed in version 3.6: Grouping digits with underscores as in code literals is allowed.

Changed in version 3.7: x is now a positional-only parameter.

Changed in version 3.8: Falls back to \_\_index\_\_() if \_\_float\_\_() is not defined.