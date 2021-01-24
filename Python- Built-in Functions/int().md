# int()

## 목차

* 파이썬 공식 문서 - 한글(번역)
    
* 파이썬 공식 문서 - 영어(원문)

## 파이썬 공식 문서 - 한글(번역)

주어진 인자를 int 인스턴스로 반환합니다.

## 파이썬 공식 문서 - 영어(원문)

`class int([x])`<br>
`class int(x, base=10)`

Return an integer object constructed from a number or string *x*, or return `0` if no arguments are given.<br>
If *x* defines [\_\_int\_\_()](https://docs.python.org/3/reference/datamodel.html#object.__int__), `int(x)` returns `x.__int__()`. If *x* defines [\_\_index\_\_()](https://docs.python.org/3/reference/datamodel.html#object.__index__), it returns `x.__index__()`. If *x* defines [\_\_trunc\_\_()](https://docs.python.org/3/reference/datamodel.html#object.__trunc__), it returns `x.__trunc__()`.<br>
For floating point numbers, this truncates towards zero.

If *x* is not a number or if *base* is given, then *x* must be a string, [bytes](https://docs.python.org/3/library/stdtypes.html#bytes), or [bytearray](https://docs.python.org/3/library/stdtypes.html#bytearray) instance representing an [integer literal](https://docs.python.org/3/reference/lexical_analysis.html#integers) in radix *base*.<br>
Optionally, the literal can be preceded by `+` or `-` (with no space in between) and surrounded by whitespace.<br>
A base-n literal consists of the digits 0 to n-1, with `a` to `z` (or `A` to `Z`) having values 10 to 35.<br>
The default *base* is 10.<br>
The allowed values are 0 and 2–36. Base-2, -8, and -16 literals can be optionally prefixed with `0b/0B`, `0o/0O`, or `0x/0X`, as with integer literals in code.<br>
Base 0 means to interpret exactly as a code literal, so that the actual base is 2, 8, 10, or 16, and so that `int('010', 0)` is not legal, while `int('010')` is, as well as `int('010', 8)`.

The integer type is described in [Numeric Types -- int, float, complex](https://docs.python.org/3/library/stdtypes.html#typesnumeric).

------

Changed in version 3.4: If base is not an instance of int and the base object has a [base.\_\_index\_\_](https://docs.python.org/3/reference/datamodel.html#object.__index__) method, that method is called to obtain an integer for the base.<br>
Previous versions used [base.\_\_int\_\_](https://docs.python.org/3/reference/datamodel.html#object.__int__) instead of [base.\_\_index\_\_](https://docs.python.org/3/reference/datamodel.html#object.__index__).

Changed in version 3.6: Grouping digits with underscores as in code literals is allowed.

Changed in version 3.7: x is now a positional-only parameter.

Changed in version 3.8: Falls back to [\_\_index\_\_()](https://docs.python.org/3/reference/datamodel.html#object.__index__) if [\_\_int\_\_()](https://docs.python.org/3/reference/datamodel.html#object.__int__) is not defined.