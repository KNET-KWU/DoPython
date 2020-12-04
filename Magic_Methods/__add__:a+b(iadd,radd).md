# add, iadd, radd

평소 인지하기 않았겠지만, 더하기 기호 `+` 도 `a`같은 문자 중 하나입니다.

우리가 일상적으로 쓰는 '더하기' 기호 `+`를 메타적으로 생각해보면 단지 기호일 뿐인데 수학의 기능이 있는 겁니다.

파이썬에서 더하기 `+`는 메서드라기보다 코드 텍스트(Raw Code)에 있는 `+` 텍트를 `__add__`로 치환한다고 생각하시면 됩니다.<br>
매크로처럼

```python
1 + 2
# is same as
int.__add__(1, 2)
```

# 목차

* 숫자처럼 더할 수 있는 나만의 타입(클래스) 만들기

* 놓치면 안되는 것들

    * 논리적이고, 사용자를 배려하는 연산자 재정의하기
    
        * 적절한 에러 던지기
        
        * 다른 타입간에 보정하기

    * return self
    
* \_\_radd\_\_
    
* \_\_iadd\_\_

* 코드로 정리

# 숫자처럼 더할 수 있는 나만의 타입 만들기

```python

```

<br>

# 놓치면 안되는 것들

단순히 `__add__`만 정의한다고 더하기 연산자가 제대로 작동한다고 볼 수 없습니다.

특히 다른 타입간에 더하기를 했을 때 **적절한 예외**를 던지거나, **다른 타입간에 보정**을 해줘야할 필요가 있죠

<br>

## 사용자를 배려하는 연산자 재정의하기

*적절한 예외*는 커스텀 타입을 이용하는 *사용자 관점*에서 직관적으로 옳다고 생각했다는 사용방법이 틀렸음을 알려주는 것입니다.

특히나 '더한다'라는 상식적인 개념이 통용되는 산술 연산자의 경우(`-`, `/`, `*`, 등) 혼동이 더 잘 일어날 수 있기 때문에 미래의 사용자를 위해 친절하게 작성할 필요가 있습니다.

제작자 입장에서 문서화를 했고, 사용자가 문서를 읽었다면 다행이지만 양쪽 다 수행할 확률이 낮기 때문에 이해가능한 에러 코드를 작성할 필요가 있습니다.

<br>

### 적절한 에러 던지기

예시를 위해서 벡터를 표현한 클래스로 예시를 들어보겠습니다.

이번 예시만을 위해서 시작하는 좌표를 클래스 속성에 포함시키겠습니다.

```python
from math import sqrt, pow

class Vector:
    def __init__(self, vx, vy, coordinate : tuple = (0,0)):
        self.vx = vx
        self.vy = vy
        self.coordinate = coordinate

    def __updateVector(self, vector):
        self.vx += vector.vx
        self.vy += vector.vy

    def __add__(self, other):
        if isinstance(other, Vector):
            self.__updateVector(other)
        else:
            raise Exception(f"Only adding type 'Vector' is allowed / given : {type(other)}")
```

클래스 구조가 한눈에 보이시나요? 있을 법한 사용사례를 통해서 설명하겠습니다.

```python
v1 = Vector(1, 5, (2,2))

v1 = v1 + (4, 5)    # Exception: Only adding type 'Vector' is allowed / given : <class 'tuple'>
```

직관적으로 봤을 때 벡터라는 클래스에 튜플을 더하면 (4, 5) 크기의 벡터를 더하는 것이라고 생각할 수 있습니다.

직관적일수 있지만, 코드가 길어지면 이게 튜플에 튜플을 더하는 것인지 벡터에 벡터를 더하는 건지 읽기 어렵겠죠.

타입 혼동을 방지하기 위해 예외를 던지고, 사유를 친절하게 쓰는 것이 본인을 포함한 다른 사용자를 위한 것입니다.

<br>

### 다른 타입간 보정해주기

'적절한 예외 던지기'와 반대로 **직관**적으로 당연하게 더해질 것 같은 것들을 지원할 경우 조건 별로 나눠주는 것이 중요합니다.

```python
# 현금을 의미하는 Cash, 카드를 의미하는 Card 클래스는 생략하겠습니다

class Wallet:
    def __init__(self, cash : Cash = Cash(), cardSet = set()):
        self.cash = cash
        self.cardSet = cardSet
        
    def __add__(self, other):
        if isinstance(other, Cash):
            self.cash +=  other.amount
            # ... 여러 작업들 ...
            
        elif isinstance(other, Card):
            self.cardSet += other
            # ... 여러 작업들 ...
            
        else:
            raise Exception(f"Only adding type 'Cash', 'Card' is allowed / Given : {type(other)}")
            
ccppooWallet = Wallet(Cash(100_000), (Card('KB'), Card('KaKao')))

ccppooWallet += Cash(5_000)

ccppooWallet += 3_000   # Exception: Only adding type 'Cash', 'Card' is allowed / Given : <class 'int'>
```

일반적으로 프로그래밍을 하신 분이라면 `addCash` 또는 `addCard`와 같은 기능을 한눈에 알아볼 수 있는 메서드를 작성하실 수 있을 겁니다.

위 예시처럼 연산자를 이용하는 것이 직관적이고, 모두가 더 편리하게 알아볼 수 있다면 취사에 따라 선택하시면 됩니다.

<br>

## return self

무언가를 더할 때 한번만 더하는 건 아닙니다.

이런건 평소 문자열을 다룰 때도 익숙했죠

```python
myEmail = 'blahblah' + '@' + 'gmail' + '.com'
```

위 예시에선 제가 `__add__`를 정의했을 때 반환문을 안쓴 것을 눈치 채셨나요?

일반적으로 우리가 더하기를 할 때 간과하는 점이 있습니다.

더하기가 여러차례 있는 경우 중간결과가 생긴다는 점이죠. `myEmail` 문장이 구해지는 과정을 나열하면 아래와 같습니다.

```python
myEmail = 'blahblah' + '@' + 'gmail' + '.com'

# 1
myEmail = 'blahblah@' + 'gmail' + '.com'

# 2
myEmail = 'blahblah@gmail' + '.com'

# 3
myEmail = 'blahblah@gmail.com'
```

더하는 과정이 한번에 완료되는 것이 아니라 차례대로 더해지는 것이죠.

여기서 `__add__` 메서드에 반환문이 없다면 다음과 같이 됩니다.

`1+2+3+4`와 같은 더하기에는<br>
1-(1) `3 = 1 + 2`, 1-(2) `3 + 3 + 4`<br>
2-(1) `6 = 3 + 3`, 2-(2) `6 + 4`<br>
3-(1) `10 = 6 + 4` 3-(2) `10`

이렇게 중간 과정이 있는 것을 꼭 기억하시길 바랍니다

```python
myEmail = 'blahblah' + '@' + 'gmail' + '.com'

# 1 - 1
myEmail = ('blahblah@'이 만들어짐 - __add__ 메서드 반환해야함) + 'gmail' + '.com'

# 1 - 2, 계속해서 더해야할 것이 뿅하고 사라집니다 
myEmail = None + 'gmail' + '.com'
```

위에 들었던 벡터 예시로 보여드리겠습니다.

계속 더해야할 것들이 계속 존재하지 않고 계산식에서 사라집니다. `None`이 계산식 가운데 떡하니 놓이게 되는 것이죠

```python
v1 = Vector(1, 3)

v2 = v1 + Vector(2, 3) + Vector(5,6)    # TypeError: unsupported operand type(s) for +: 'NoneType' and 'Vector'

# 에러가 난 상황은 아래와 같습니다
v2 = None + Vector(5,6)
```

직관적으로 생각한 행동(더하기를 수행하면서 당연히 됐을거라는 기대)를 직접 구현해보겠습니다

```python
from math import sqrt, pow

class Vector:
    def __init__(self, vx, vy, coordinate : tuple = (0,0)):
        self.vx = vx
        self.vy = vy
        self.coordinate = coordinate

    def __updateVector(self, vector):
        self.vx += vector.vx
        self.vy += vector.vy

    def __add__(self, other):
        if not isinstance(other, Vector):
            raise Exception(f"Only adding type 'Vector' is allowed / given : {type(other)}")
            
        self.__updateVector(other)
        
        return self        
        '''
        또는 새로운 벡터 객체를 반환합니다
        return Vector(self.vx, self.vy)
        '''
```

하면 다음과 같이 **직관적**으로 더하기가 작동합니다

```python
v1 = Vector(1, 3)

v2 = v1 + Vector(2, 3) + Vector(5,6)

print(v2)   # vector : cord(8, 12)
```

<br>

## \_\_radd\_\_

add, `+`에 오른쪽에 있는 인스턴스를 호출하는 메서드입니다.

관례적으로 `__radd__`가 호출되는 순서는  아래와 같습니다.

```python
a1 + a2 

# 첫번째 호출 : a1.__add__(a2)
# 두번째 호출 : a2.__radd__(a1)

a2가 + 연산자 오른쪽에 있으니깐 __radd__를 호출하는 인스턴스는 a2, 인자로 a1이 대입됩니다.
```

계속해서 Vector 클래스를 이용한 예시를 보여드리겠습니다.

```python
class Vector:
    # __add__에 정의된 Vector에 이어서
    
    def __add__(self, other):
        # ... other이 Vector 인스턴스인지 확인
        return other.__radd__(self)
    
    def __radd__(self, other):
        self.__updateVector(other)
        return self
```

사람마다 하는 설계하는 방식은 다르며, `__add__`에서 최종적으로 연산을 하는 부분을 `__radd__` 또는 `__iadd__`에서 정의하는 방식이 있습니다.

<br>

## \_\_iadd\_\_

`a += b` 를 쓰는 경우에 해당되는 연산자입니다.

호출되는 방식은 아래와 같습니다.

```python
a += b
a.__iadd__(b)
```

<br>

# 코드로 정리

```python
class A:
    def __init__(self, name):
        self.name = name
    
    def __add__(self, other):
        print('add')
        print(self)
        return self
        
    def __radd__(self, other):
        print('radd')
        print(self)
        return self

    def __iadd__(self, other):
        print('iadd')
        print(self)
        return self
        
    def __repr__(self):
        return f'{self.name}'
        
A('yy') + A('zz')

a , b= A('a'), A('b') 
a += b
```

결과 

```python
add
yy -> __add__의 self는 + 왼쪽에 있는 A('yy')

iadd
a -> __iadd__의 self는 += 왼쪽에 있는 a
```