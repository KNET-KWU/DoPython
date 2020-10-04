# hex()

## 목차

* 파이썬 공식 문서 - 한글(번역)
    
* 파이썬 공식 문서 - 영어(원문)

## 파이썬 공식 문서 - 한글(번역)

hex(x)

인자 *x*를 "0x"로 시작하고 소문자(0~f)로 구성된 16진수 문자열로 반환합니다.

*x*가 파이썬 내장 자료형 정수([int](https://docs.python.org/3/library/functions.html#int))가 아닌 객체의 경우, 정수를 반환하는 [\_\_index\_\_()](https://docs.python.org/3/reference/datamodel.html#object.__index__)가 정의되어야합니다.

```python
hex(255)    # '0xff'
hex(-42)    # '-0x2a'

class MyClass:
    def __index__(self):
        return 255
        
hex(MyClass())  # '0xff'
```

인자로 주어진 정수가 16진수로 변환되면서 대문자로 구성되거나, '0x'가 앞에 안붙여진 16진수로 변환되길 원한다면<br>
`format()` 메서드를 이용해 바꿀 수 있습니다.

```python
# 문자열 포멧을 이용하는 경우 (1)
'%#x' % 255' # '0xff'
'%x' % 255' # 'ff'
'%X' % 255'  # 'FF'

# 문자열 포멧을 이용하는 경우 (2)
f'{255:#x}' # '0xff'
f'{255:x}'  # 'ff'
f'{255:X}'  # 'FF'

# format() 메서드를 이용하는 경우 
format(255, '#x')   # '0xff'
format(255, 'x')    # 'ff'
format(255, 'X')    # 'FF'
```

파이썬 내장 함수 `int()`를 이용해서 16진수를 정수로 변환할 수 있습니다.

```python
hex(255)    # 0xff
int('0xff', 16) # 255
```

실수(float)를 16진수로 변환하는 경우 `float.hex()`를 사용하시면 됩니다.

```python
flt = 12.33

# 아래 세가지 표현 모두 가능합니다
flt.hex()   # 0x1.8333333333333p
12.1.hex()  # 0x1.8333333333333p
(12.1).hex()    # 0x1.8333333333333p
```


## 파이썬 공식 문서 - 영어(원문)

hex(x)

Convert an integer number to a lowercase hexadecimal string prefixed with "0x".<br>
If x is not a Python [int](https://docs.python.org/3/library/functions.html#int) object, 
it has to define an [\_\_index\_\_()](https://docs.python.org/3/reference/datamodel.html#object.__index__) method that returns an integer.<br>
Some examples:

```python
>>>
>>> hex(255)
'0xff'
>>> hex(-42)
'-0x2a'
```

If you want to convert an integer number to an uppercase or lower hexadecimal string with prefix or not, you can use either of the following ways:

```python
>>>
>>> '%#x' % 255, '%x' % 255, '%X' % 255
('0xff', 'ff', 'FF')
>>> format(255, '#x'), format(255, 'x'), format(255, 'X')
('0xff', 'ff', 'FF')
>>> f'{255:#x}', f'{255:x}', f'{255:X}'
('0xff', 'ff', 'FF')
```

See also [format()](https://docs.python.org/3/library/functions.html#format) for more information.

See also [int()](https://docs.python.org/3/library/functions.html#int) for converting a hexadecimal string to an integer using a base of 16.

Note To obtain a hexadecimal string representation for a float, use the `float.hex()` method.