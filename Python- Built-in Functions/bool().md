# bool()

## 목차

* 파이썬 공식 문서 - 한글(번역)
        
    * 파이썬 내장 타입 - Boolean Values
    
    * 참값 판별 과정이란? - Truth Testing Procedure

    * bool의 내장 메서드?

* 파이썬 공식 문서 - 영어(원문)

## 파이썬 공식 문서 - 한글(번역)

불린(Boolean) 값인 `True`와 `False` 둘중 하나를 반환합니다.

인자로 주어진 x는 파이썬의 표준 [*참값 판별 과정(truth testing procedure)*](https://docs.python.org/3/library/stdtypes.html#truth-value-testing)을 통해 변환됩니다.<br>
(아래에 설명 첨부)

여기서 x가 거짓(false) 또는  못함(omitted)일 경우 `False`를 반환하고, 반대의 경우 `True`를 반환합니다.

`bool` 클래스는 `int`의 서브클래스입니다.<br>
([Numeric Types - int, float, complex](https://docs.python.org/3/library/stdtypes.html#typesnumeric)를 참고하세요)

```python
issubclass(bool, int)   # True

issubclass(type(False), int) # True
issubclass(type(True), int) # True
```

`bool` 클래스로 서브클래스를 만들 수 없습니다.

```python
class cool(bool):
    pass

TypeError("type 'bool' is not an acceptable base type")
```

`bool` 클래스의 인스턴스는 `False`와 `True`가 유일합니다.<br>
([Boolean Values](https://docs.python.org/3/library/stdtypes.html#bltin-boolean-values)를 참고하세요 - 아래에서 설명합니다)

파이썬 버전 3.7 변화:

x는 이제 **positional-only parameter**<br>
(파라미터의 이름 없이 순서에 따라 대입하면 되는 것입니다, 자세한건 찾아보세요!)

### 파이썬 내장 타입 - Boolean Values

파이썬 공식 문서 : [Boolean Values](https://docs.python.org/3/library/stdtypes.html#bltin-boolean-values)

불린 값은 False와 True 두가지가 유일하게 존재하는 상수 객체입니다.

불린 값은 참값을 판별하기 위해 사용되며, 불린 값외에 다른 객체들 또한 참/거짓을 판별할 수 있는 방법을 통해 불린 연산을 할 수 있습니다.<br>
('참값 판별 과정이란? - Truth Testing Procedure'를 보세요)

수의 개념으로써 True는 1, False는 0으로 취급됩니다.

### 참값 판별 과정이란? - Truth Testing Procedure

파이썬 공식 문서 : [Truth Value Testing](https://docs.python.org/3/library/stdtypes.html#truth-value-testing)

모든 객체는 `bool(객체)`를 사용하여 참값의 확인할 수 있습니다.

참값 테스트의 값은 if 조건문 또는 while 조건문 판별 또는 불린(Boolean) 연산자의 오퍼랜드(operand)로 쓰일 수 있습니다.

```python
# 여기서 object는 모든 것이 될 수 있습니다.

if(object):
    # ...

while(object):
    # ...
    
# Boolean 연산자 - and, or, not

# A and B 연산 : if A is false, then B, else A
'ccppoo' and None   # None

# A or  B 연산 : if A is false, then A, else B
'ccppoo' or None    # 'ccppoo'

# not A 연산 : if A is false, then False, else True
not 'ccppoo'        # False
```

파이썬 이전에 다른 언어를 배웠다면 파이썬의 and, or 연산자가 불 값(True, False)를 반환하지 않고<br>
삼항연산자처럼 작동하는 것을 알 수 있을 것이다.

and 연산자와 or 연산자는 파이썬의 삼항연산자와 *비슷하게* 작동하긴 한다

```python
# 1. and 연산자와 파이썬 삼항연산자와 삼항연산자
'ccppoo' and None # None

'ccppoo' if not 'ccppoo' else None # None

# B ? A : B - 파이썬 and가 작동하는 방법

# 2. or  연산자와 파이썬 삼항연산자와 삼항연산자
'ccppoo' or None # 'ccppoo'

'ccppoo' if 'ccppoo' else None # 'ccppoo

# (A || B) ? A : B - 파이썬 or이 작동하는 방법
```

기본값으로, 객체는 \_\_bool\_\_()를 재정의해서 False를 반환하거나, \_\_len\_\_() 메서드를 호출했을 때 0을 반환하는 경우 외에는 True를 반환합니다.

```python
class C1():
    def __bool__(self) -> bool:
        print('bool called')
        return False
    def __len__(self) -> bool:
        print('len From C1 is called')
        return 0

class C2():
    def __len__(self) -> int:
        print('len called')
        return 0

c1 = C1(); c2 = C2()
if not c1:
    print('c1 is false')

if not c2:
    print('c2 is false')

< bool called
< c1 is false
< len called
< c2 is false
```

위 예시에서 참값 판단의 순서는 bool -> len 함수임을 알 수 있습니다.

아래는 파이썬 내장 객체 중 False 값으로 판별되는 객체들입니다.

* false로 정의되는 상수들 : None, False
* 숫자 자료형 중 0인 것들: 0 (정수), 0.0 (실수), 0j (복소수), 0, (0,1) - 분수
* 비어있는 연속된 자료형: 빈 문자열( ''), 빈 튜플 (), 빈 리스트( []), 빈 세트 (set()), range(0)

불린 타입의 값을 가지고 있는 파이썬 연산자와 내장 함수들은 반드시 false를 의미하는 0 또는 False를 반환하고 true를 의미하는 1 또는 True를 반환합니다.

반환하지 않는 경우 문서에 따로 명시가 되어있습니다.

예외적으로 앞서 다룬 불린 연산자 `and`와 `or`는 0, False, 1, True 이 네가지 값 대신 불린 값을 비교한 오퍼랜드를 반환합니다.<br>
(위 예시에서 'ccppoo'하고 None을 반환하는 것 처럼)

### bool 클래스의 내장 메서드?

파이썬의 모든 것이 클래스이듯이 bool 또한 클래스입니다.

bool에는 어떤 요소가 있는지 dir()를 통해서 볼까요?

```python
dir(bool)
'''
['__abs__', '__add__', '__and__', '__bool__', '__ceil__', '__class__', '__delattr__', '__dir__', '__divmod__', '__doc__', '__eq__', '__float__', '__floor__', '__floordiv__', '__format__', '__ge__', '__getattribute__', '__getnewargs__', '__gt__', '__hash__', '__index__', '__init__', '__init_subclass__', '__int__', '__invert__', '__le__', '__lshift__', '__lt__', '__mod__', '__mul__', '__ne__', '__neg__', '__new__', '__or__', '__pos__', '__pow__', '__radd__', '__rand__', '__rdivmod__', '__reduce__', '__reduce_ex__', '__repr__', '__rfloordiv__', '__rlshift__', '__rmod__', '__rmul__', '__ror__', '__round__', '__rpow__', '__rrshift__', '__rshift__', '__rsub__', '__rtruediv__', '__rxor__', '__setattr__', '__sizeof__', '__str__', '__sub__', '__subclasshook__', '__truediv__', '__trunc__', '__xor__', 'bit_length', 'conjugate', 'denominator', 'from_bytes', 'imag', 'numerator', 'real', 'to_bytes']
'''
```

음... 단지 참값을 확인하는 내장 메서드라고 알고 있는데 생각보다 엄청많은 메서드가 있는것을 볼 수 있습니다.

이렇게 많은 메서드가 존재하는 이유는 bool 클래스가 위에서 언급했다시피 bool이 int 클래스의 인스턴스기 때문입니다.

`print(dir(int) == dir(bool)) # True`입니다. 실제로 int의 메서드를 쓸수도 있습니다.

```python
True.to_bytes(2, byteorder = 'big') # b'\x00\x01'
```

True가 정수로 1에 해당하는 값을 가지기 때문에 작동하는 건데요 굳이 문제삼을 필요는 없지만,

코드 재사용을 위한 상속을 이용해 bool이 만들어졌기 때문에 필요없는 메서드가 있는것을 알수있습니다.

아무튼 여러분은 객체지향적으로 코드를 작성하시길 바랍니다.

## 파이썬 공식 문서 - 영어(원문)

Return a Boolean value, i.e. one of `True` or `False`.<br>
x is converted using the standard *truth testing procedure*. If x is false or omitted, this returns `False`; otherwise it returns `True`.<br>
The bool class is a subclass of int (see Numeric Types - int, float, complex).<br>
It cannot be subclassed further.<br>
Its only instances are `False` and `True`.