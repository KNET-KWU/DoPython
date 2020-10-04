# oct()

## 목차

* 파이썬 공식 문서 - 한글(번역)
    
* 파이썬 공식 문서 - 영어(원문)

## 파이썬 공식 문서 - 한글(번역)

oct(x)

인자 *x*를 "0b"로 시작하는 8진수 문자열로 반환합니다.

*x*가 파이썬 내장 자료형 정수([int](https://docs.python.org/3/library/functions.html#int))가 아닌 객체의 경우, 정수를 반환하는 [\_\_index\_\_()](https://docs.python.org/3/reference/datamodel.html#object.__index__)가 정의되어야합니다.

'0o'가 앞에 안붙여진 8진수로 변환되길 원한다면 `format()` 메서드를 이용해 바꿀 수 있습니다.<br>
16진수와 달리 8진수의 경우 알파벳(A~F)이 쓰이지 않으므로 대소문자에 구분이 없습니다.

```python
# 문자열 포멧을 이용하는 경우 (1)
'%#o' % 63' # '0x77'
'%o' % 63' # '77'
'%O' % 63'  # '77'

# 문자열 포멧을 이용하는 경우 (2)
f'{63:#o}' # '0x77'
f'{63:o}'  # '77'
f'{63:O}'  # '77'

# format() 메서드를 이용하는 경우 
format(63, '#o')   # '0x77'
format(63, 'o')    # '77'
format(63, 'O')    # '77'
```

파이썬 내장 함수 `int()`를 이용해서 16진수를 정수로 변환할 수 있습니다.

```python
oct(63)    # 0o77
int('0o77', 8) # 63
```

파이썬에는 16진수와 달리 8진수는 실수(float)를 8진수로 바꿀 수 있는 메서드가 존재하지 않습니다.

```python
(12.1).hex()    # 0x1.8333333333333p
(12.1).oct()    # AttributeError: 'float' object has no attribute 'oct'

oct(12.1)   # TypeError: 'float' object cannot be interpreted as an integer
```

## 파이썬 공식 문서 - 영어(원문)

oct(x)

Convert an integer number to an octal string prefixed with "0o".<br>
The result is a valid Python expression.<br>
If *x* is not a Python int object, it has to define an \_\_index\_\_() method that returns an integer.<br>
For example:

```python
>>>
>>> oct(8)
'0o10'
>>> oct(-56)
'-0o70'
```

If you want to convert an integer number to octal string either with prefix "0o" or not, you can use either of the following ways.

```python
>>>
>>> '%#o' % 10, '%o' % 10
('0o12', '12')
>>> format(10, '#o'), format(10, 'o')
('0o12', '12')
>>> f'{10:#o}', f'{10:o}'
('0o12', '12')
```

See also [format()](https://docs.python.org/3/library/functions.html#format) for more information.
