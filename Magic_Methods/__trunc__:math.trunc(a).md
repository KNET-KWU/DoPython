# trunc

truncate : (아래나 위를 잘라서) 길이를 줄이다[짧게 하다]

## trunc 언제, 왜 쓰는 걸까?

`trunc()`는 파이썬의 기본 내장 모듈 math에 정의된 메서드입니다.

숫자와 관련된 타입(클래스)에서 숫자의 정수부분만 필요한 경우, 소수점을 제외한 숫자를 반환합니다.

```python
from math import trunc

# 정수, int
trunc(12)   # 12

# 실수, float
trunc(12.34)    # 12

# 허수, complex - 는 trunc를 지원하지 않습니다.
trunc(complex(12.34, 90))   # TypeError: type complex doesn't define __trunc__ method
```

숫자 자료형의 **올림**을 반환하는 메서드 `__ceil__`,

숫자 자료형의 **버림**을 반환하는 메서드 `__floor__`,

숫자 자료형의 **반올림**을 반환하는 메서드 `__round__`

위 메서드와 상관없이 `trunc`는 수학적인 의미없이 단지 숫자의 정수부분만을 반환한다고 이해하시면 됩니다.

```python
from math import trunc

trunc(-1.2) # -1

trunc(-1.8) # -1

trunc(1.2)  # 1

trunc(1.8)  # 1
```

<br>

`trunc` 메서드를 다음과 같이 직접 math 모듈을 통해서 사용한다면 `__trunc__`의 반환 타입 상관없이 정의할 수 있습니다.

```python
from math import trunc

class MyFloat(float):        
    def __trunc__(self):
        return 'Hello'

trunc(MyFloat(12.34))   # Hello
```

하지만 상식적으로, 그리고 관례상 정수 타입을 기대하는 메서드이기 때문에 정수를 반환하도록 정의하는 것이 정상입니다.

<br>

특정 타입에 `int()` 함수의 인자로 대입했을 때, 그 타입에 `__int__`이 정의되어 있지 않으면 `__trunc__` 메서드를 호출합니다.

```python
class MyType(str):
    # ... 
    def __trunc__(self) -> str:
        print('~~~ __trunc__ called! ~~~')
        # 예시일뿐, 지원하지 않는 타입을 사용자가 사용하려고 시도하면 Exception을 던져야합니다.
        return 'MyType doesn\'t support <type int>'

# 사용자의 코드        
print(int(MyType))

'''
반환값입니다
~~~ __trunc__ called! ~~~
TypeError: __trunc__ returned non-Integral (type str)
'''
```

`int` 생성자 함수에서는 `__trunc__` 메서드로부터 정수 타입을 기대하고 있었으나, 다른 타입이 반환되었기 때문에 에러를 던졌습니다.

**정리** : `__trunc__` 말고도 다른 메서드에서도 관례(인자 타입, 반환 타입)를 지키자.

<br>

## 설명을 추가한 원본 내용

[원문 링크](https://docs.python.org/3/reference/datamodel.html#object.__trunc__)

Called to implement the built-in function round() and math functions trunc(), floor() and ceil().<br>
Unless ndigits is passed to __round__() all these methods should return the value of the object truncated to an Integral (typically an int).

If __int__() is not defined then the built-in function int() falls back to __trunc__().