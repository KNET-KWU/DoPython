# bin()

## 목차

* 파이썬 공식 문서 - 한글(번역)
    
* 파이썬 공식 문서 - 영어(원문)

## 파이썬 공식 문서 - 한글(번역)

bin(x)

인자로 주어진 정수 `x`를 '이진수로 표현한 b0'으로 시작하는 문자열을 반환합니다.

```python
bin(10) # 0b1010
type(bin(10))   # <class 'str'>
```

이진수를 나타내는 'b0'으로 시작하는 수는 파이썬에서 이진수로 인식합니다.

```python
# 따옴표 없이 사용해야 숫자로 인식합니다
0b1010 + 0b1010 # 20

# 따옴표를 사용하면 문자열로 인식합니다
'0b1010' + '0b1010' # '0b10100b1010'
```

If x is not a Python [int](https://docs.python.org/3/library/functions.html#int) object, it has to define an [\_\_index\_\_()](https://docs.python.org/3/reference/datamodel.html#object.__index__) method that returns an integer.

bin(x) 메서드 첫번째 인자 `x`가 파이썬 정수([int](https://docs.python.org/3/library/functions.html#int)가 아닌 객체의 경우,<br>
정수를 반환하는 [\_\_index\_\_()](https://docs.python.org/3/reference/datamodel.html#object.__index__) 메서드가 정의되어야 합니다.

```python
class MyClass:
    pass    
mycls = MyClass()
bin(mycls)
# TypeError: 'C' object cannot be interpreted as an integer

class MyClass2:
    def __index__(self):
        return 10

mycls2 = MyClass2()
bin(mycls2) # 0b1010
```

[format()](https://docs.python.org/3/library/functions.html#format) 메서드를 이용해서 이진수로 '표현'할 수 있습니다.

```python
format(10, '#b')    # 0b1010
format(10, 'b')     # 1010

f'{10:#b}'  # 0b1010
f'{10:b}'   # 1010
```

## 파이썬 공식 문서 - 영어(원문)

bin(x)

Convert an integer number to a binary string prefixed with "0b".<br>
The result is a valid Python expression.<br>
If x is not a Python [int](https://docs.python.org/3/library/functions.html#int) object, it has to define an [\_\_index\_\_()](https://docs.python.org/3/reference/datamodel.html#object.__index__) method that returns an integer. Some examples:

```python
bin(3) # '0b11'
bin(-10) # '-0b1010'
```

If prefix "0b" is desired or not, you can use either of the following ways.

```python
format(14, '#b'), format(14, 'b') #('0b1110', '1110')
f'{14:#b}', f'{14:b}' # ('0b1110', '1110')
```

See also [format()](https://docs.python.org/3/library/functions.html#format) for more information.