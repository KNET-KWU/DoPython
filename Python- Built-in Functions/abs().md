# abs()

abs는 absolute의 약자로 인자로 주어진 숫자의 절댓값을 반환합니다.

## 목차

* 파이썬 공식 문서 - 한글(번역)
    
* 파이썬 공식 문서 - 영어(원문)

## 파이썬 공식 문서 - 한글(번역)

abs(x)

숫자의 절댓값을 반환합니다.

인자는 정수 또는 부동소수점일 수 있습니다.

```python
abs(-42)    # 42
abs(20)     # 20
abs(-4.2)   # 4.2
```

주어진 인자가 복소수일 경우, 복소수의 magnitude를 반환합니다.

```python
complex(3,-2))  # (3-2j)
complex(-3, 2)) # (-3+2j)

abs(complex(-3, -2))    # 3.605551275463989
abs(complex(-3, 2)) # 3.605551275463989
abs(complex(3, -2)) # 3.605551275463989
abs(complex(3, 2))  # 3.605551275463989
```

사용자 정의 클래스에서 \_\_abs\_\_()를 정의한 경우, `abs(x)`는 `x.\_\_abs\_\_()`를 반환합니다.

### 내가 만든 클래스에서 abs() 재정의하기

abs()는 사용자가 재정의 할 수 있는 매직메서드 중 하나입니다.

본인이 숫자와 관련된 클래스, 혹은 숫자의 개념이 있는 클래스를 정의할 때 abs를 재정의해서 본인의 입맛에 사용할 수 있습니다.

아무래도 절댓값을 반환하는 메서드이니 숫자와 관련된 클래스를 재정의 하는 편이 좋겠죠?

```python
from math import sqrt, pow

class Vector2D:
    def __init__(self, dx : float, dy : float):
        self.dx = dx
        self.dy = dy
        
    def __abs__(self):
        # 벡터의 절댓값을 반환하면 크기를 반환한다.
        return sqrt(pow(dx,2) + pow(dy, 2))

v = Vector2D(4, 3)
abs(v)  # 5.0
```

abs가 absolute의 약자라고 했지만, 본인이 abs가 다른 단어의 약자라고 생각하면 그 용도에 맞게 사용하셔도 상관없습니다.

```python
class Student:
    def __init__(self, name:str, attend: bool = True):
        self.name = name
        self.attend = attend
        
    # abs를 absent(결석) 이라고 생각하고 재정의해서 사용할 수도 있습니다. - 숫자와 관련 없어도 됩니다.
    def __abs__(self):
        self.attend = False
        print(f'{self.name} is absent')

student1 = Student('ccppoo')

abs(student1)   # ccppoo is absent
```

## 파이썬 공식 문서 - 영어(원문)

Return the absolute value of a number.<br>
The argument may be an integer or a floating point number.<br>
If the argument is a complex number, its magnitude is returned.<br>
If x defines \_\_abs\_\_(), `abs(x)` returns `x.\_\_abs\_\_()`.