# complex()

## 목차

* 파이썬 공식 문서 - 한글(번역)

  * 복소수도 숫자입니다, 사실 생성자 같은 건 필요없어

  * 켤레복소수, conjuagte

* 파이썬 공식 문서 - 영어(원문)

## 파이썬 공식 문서 - 한글(번역)

class complex([real[, imag]])

Return a complex number with the value *real* + *imag*\*1j or convert a string or number to a complex number.<br>

*real*+*imag*\*1j, '실수 + 허수j' 형태로 된 복소수를 반환합니다.

복소수 형식("n+mj")의 문자열이 주어져도 복소수로 반환합니다.

```python
# 실수는 20이고, 허수는 3인 복소수
complex(20, 3) # (20+3j)

complex("20+3j") # (20+3j)

complex(20, 3) == complex("20+3j") # True

complex('ccppoo') # ValueError: complex() arg is a malformed string
```

첫번째 인자가 문자열인 경우, 반드시 첫번째 인자 외에 다른 인자는 없어야 하고 문자열은 복소수로 해석됩니다.

```python
complex(20) # (20+0j)

complex('20', '3') 
TypeError: complex() can't take second arg if first is a string
```

첫번째 인자가 숫자인 경우에 두번째 인자로 문자열이 올 수 없습니다.

```python
complex(20, '3')
TypeError: complex() second arg can't be a string
```

모든 인자는 복소수를 포함한 숫자 타입이 될 수 있습니다.

`complex(real, imag)` 에서 복소수를 인자에 대입하는 경우 작동하는 방식이 재밌습니다.

```python
cnum = complex(4, 5) # (4+5j)

complex(cnum, 8) # (4+13j)

complex(8, cnum) # (3+4j)
```

두개의 인자가 주어지면 ([real] + [imag]*j)과 같이 연산이 되는 것입니다.

첫번째 예시는 (4+5j) + 8*j = 4 + 13j

두번째 예시는 8 + (4+5j)*j = 8 + 4j + (-5) = 3 +4j

...

허수에 해당하는 두번째 인자 *imag*가 주어지지 않으면 기본값으로 0이 사용됩니다.

아무 인자 없이 `complex()` 만 사용되는 경우, `0j`를 반환합니다.

```python
complex(20) # (20+0j)

complex() # 0j
```

사용자가 정의한 타입, 클래스 객체를 인자로 대입한 경우, `complex(x)`는 `x.\_\_complex\_\_()` 호출하는 것과 같습니다.

`x`의 클래스 타입에 \_\_complex\_\_()가 정의되어있지 않다면 \_\_float\_\_() -> \_\_index\_\_() 순서로 호출합니다.

세가지 메서드가 모두 정의되어 있지 않다면 TypeError를 던집니다.

```python
# 1.아무것도 없는 경우
class MyClass:
 pass
 
complex(MyClass()) 
TypeError: complex() first argument must be a string or a number, not 'MyClass'

# 2. __complex__(), __float__(), __index__()가 정의 되어있는 경우
class MyClass2:
 def __init__(self, r, i):
  self.r = r
  self.i = i
  
 # __float__은 반드시 float을 반환해야 합니다.
 def __float__(self) -> float:
  print("call : 'float'")
  return float(self.r)
  
 def __index__(self):
  print("call : 'index'")
  return self.r
  
 def __complex__(self) -> complex:
  print("call : 'complex'"
  # 파이썬 기본 메서드 complex를 호출합니다. 
  return complex(self.r, self.i)

 # 이건 호출이 안됩니다 (self.complex가 아니니깐...)
 def complex(self, r, i):
  raise TypeError()
  
complex(MyClass2(4, 5))
< "call : 'complex'"
< (4+5j)
```

### 복소수도 숫자입니다, 사실 생성자 같은 건 필요없어

복소수는 파이썬의 내장 숫자 int, float과 같은 타입중 하나입니다.

복소수를 선언할 때 평범한 숫자처럼 써도 됩니다. 단지 2,8,16 진수처럼 앞뒤로 무언가가 붙여야 한다는 것을 제외한다면요.

```python
# 8진수, 16진수
0o123, 0x123  # (83, 291) 이렇게 앞에 prefix를 붙이듯이

# 복소수에 해당하는 부분은 j 또는 J 를 postfix를 붙이면 됩니다
2 + 3j # (2+3j)
3+4J # (3+4j)

2 + 3j + 4 + 5j # 6 + 8j
```

사칙연산(+, -, /, *)과 제곱 연산( pow, \*\*)이 가능합니다.

```python
(2+3j) ** 2 # -5 + 13j
```

### 켤레복소수, conjuagte

정직하게 켤레복소수를 반환하는 내장 메서드가 존재합니다.

```python
(2 + 3j).conjugate() # (2-3j)
```

----------

파이썬 버전 3.6 변화: <br>

언더스코어('_')로 표현한 숫자도 인자로 쓸 수 있습니다

```python
complex(200_000, 1_000) # (200000+1000j)
```

파이썬 버전 3.8 변화: <br>

인자에 주어진 객체에 \_\_complex\_\_(), \_\_float\_\_()가 정의되어 있지 않으면 \_\_index\_\_() 메서드가 호출됩니다.



## 파이썬 공식 문서 - 영어(원문)

class complex([real[, imag]])

Return a complex number with the value *real* + *imag\*1j* or convert a string or number to a complex number.<br>
If the first parameter is a string, it will be interpreted as a complex number and the function must be called without a second parameter.<br>
The second parameter can never be a string.<br>
Each argument may be any numeric type (including complex).<br>
If *imag* is omitted, it defaults to zero and the constructor serves as a numeric conversion like [int](https://docs.python.org/3/library/functions.html#int) and [float](https://docs.python.org/3/library/functions.html#float).<br>
If both arguments are omitted, returns `0j`.

For a general Python object `x`, `complex(x)` delegates to `x.\_\_complex\_\_()`.<br>
If `\_\_complex\_\_()` is not defined then it falls back to [\_\_float\_\_()](https://docs.python.org/3/reference/datamodel.html#object.__float__).<br>
If `\_\_float\_\_()` is not defined then it falls back to [\_\_index\_\_()](https://docs.python.org/3/reference/datamodel.html#object.__index__).


~~~

Note : When converting from a string, the string must not contain whitespace around the central `+` or `-` operator.<br>
For example, `complex('1+2j')` is fine, but `complex('1 + 2j')` raises ValueError.

~~~

The complex type is described in [Numeric Types -- int, float, complex](https://docs.python.org/3/library/stdtypes.html#typesnumeric).

Changed in version 3.6: <br>
Grouping digits with underscores as in code literals is allowed.

Changed in version 3.8: <br>
Falls back to [\_\_index\_\_()](https://docs.python.org/3/reference/datamodel.html#object.__index__)
 if [\_\_complex\_\_()](https://docs.python.org/3/reference/datamodel.html#object.__complex__) and 
 [\_\_float\_\_()](https://docs.python.org/3/reference/datamodel.html#object.__float__) are not defined.