# sum()

## 목차

* 파이썬 공식 문서 - 한글(번역)
    
    * sum()은 정수 더할 때 쓰자...

        * 문자열(str) 더하기

        * 실수(float) 더하기

        * 이터러블(iterable) 합치기
    
    * 내가 만든 클래스 sum() 사용할 수 있게 만들기 - \_\_add\_\_(), \_\_radd\_\_()

* 파이썬 공식 문서 - 영어(원문)

## 파이썬 공식 문서 - 한글(번역)

`sum(iterable, /, start = 0)`

`sum()` 메서드의 두번째 인자 *start*를 시작으로 첫번째 인자인 *iterable*의 모든 요소를 더한 값을 반환합니다.

```python
# 3번째 인자부터 끝까지 더한 것

sum([0,1,2,3,4,5])   # 15

sum([0,1,2,3,4,5], 0)   # 15

sum([0,1,2,3,4,5], 3)   # 18
```

정수 타입 외에 다른 타입도 sum으로 쓸 수 있는 방식은 아래에 설명되어 있습니다.

### sum()은 정수 더할 때 쓰자...

`sum()`은 정수뿐만 아니라, 실수, 문자열에도 쓸 수 있지만 더 나은 대안이 존재합니다.

#### 문자열(str) 더하기

여러개의 **문자열**을 더하는 경우, 더 빠르게 문자열을 합치는 방법은 `''.join(sequence)` 입니다.

```python
''.join(['Hello',',', 'my', 'name', 'is', 'ccppoo'])    # 'Hello,mynameisccppoo'

' '.join(['Hello',',', 'my', 'name', 'is', 'ccppoo'])    # Hello , my name is ccppoo'
```

문자열 타입(str), 즉 **클래스(class)의 메서드**입니다.

파이썬을 하다가도 모든 것이 클래스라는 것을 잊고, 단지 문자열도 클래스임을 가끔 잊을 수 있습니다.

위 예시로 든 `''.join( ... )`에서 `''`는 길이 0의 문자열입니다.

정확히 말하면 선언과 동시에 아무것도 없는('') 문자열 인스턴스가 생성되고, 인스턴스의 메서드 join()을 사용한 것입니다.

문자열을 더할 땐 `+`를 사용할 수 있지만, 문자열 이터러블의 경우 join을 사용하는 것을 권장드립니다.

#### 실수(float) 더하기

여러개의 **실수**를 더하는 경우 더 나은 정교함을 위해서 [math.fsum()](https://docs.python.org/3/library/math.html#math.fsum)을 사용하는것을 권장드립니다.

```python
import math

# 0.1이 10개 있는 리스트
floats = [.1, .1, .1, .1, .1, .1, .1, .1, .1, .1 ]

sum(floats) # 0.9999999999999999
math.fsum(floats)   # 1.0
```

예시를 보면 왜 fsum을 써야하는지 알겠죠?

#### 이터러블(iterable) 합치기

여러개의 **이터러블**을 더하는 경우, [itertools.chain()](https://docs.python.org/3/library/itertools.html#itertools.chain)를 사용하는 것을 권장드립니다.

```python
from itertools import chain

items = chain(['Hello', 'my', 'name'], ['is', 'ccppoo'])
for x in items:
    print(x, end = ' ')

# Hello my name is ccppoo
```

대부분 리스트와 같은 이터러블을 합치거나 나누고 나서, 나눈 이터러블을 이터러블처럼 사용하기(for-loop 이용) 때문에 의식하지 않아도 위와 같이 제대로 작동합니다.

위의 `itertools.chain()` 메서드의 예시코드는 다음과 같습니다.

```python
def chain(*iterables):
    # chain('ABC', 'DEF') ---> A B C D E F
    for it in iterables:
        for element in it:
            yield element
```

`yield` 문이 보이나요?

지연 평가(lazy execution)이 이용되기 때문에 이터러블을 `chain`을 이용했을 때

우리가 재료로 넣었던 `['Hello', 'my', 'name'], ['is', 'ccppoo']`과 같은 리스트가 아닙니다.

```python
# 위 예시 코드에 이어서

# 기대했던 결과
print(items)    # ['Hello', 'my', 'name', 'is', 'ccppoo']

# 실제 결과
print(items)    # itertools.chain object at 0x11ae791d0>
```

그리고 yield 구문을 보고 아실 수 있겠지만, 한번 사용하면 모두 소진되는 **제너레이터**입니다.

즉, 한번 사용하고 또 호출하면 아무것도 안남습니다.

```python
from itertools import chain

items = chain(['Hello', 'my', 'name'], ['is', 'ccppoo'])
for x in items:
    print(x, end = ' ') # Hello my name is ccppoo
    
for x in items:
    print(x, end = ' ') # 아무것도 출력이 안됩니다.
```

재사용을 할 이터러블의 경우 새로 list로 만드신 후 사용해야합니다.<br>
(파이썬 입문 단계일 때 제너레이터 타입으로 반환하는 객체를 다루면서 많이 겪었던 문제라 추가했습니다... - 어? 왜 리스트가 비어있지?)

```python
# 무언가 연속된 데이터를 다루는 메서드가 제너레이터(yield) 형태로 반환하는데 재사용을 해야하면...

# list로 새로 생성한 뒤 사용합니다
items = list(chain(['Hello', 'my', 'name'], ['is', 'ccppoo']))
```

### 내가 만든 클래스 sum() 사용할 수 있게 만들기 - \_\_add\_\_(), \_\_radd\_\_()

`sum()`을 처음 접했을 때 '**클래스도 sum() 사용할 수 있게 만드면 괜찮겠네**?'라고 생각하신 분을 위한 목차입니다.

`sum()`의 선택적인 두번째 인자 'start'의 기본 값은 '0'입니다.

그리고 다음과 같이 작동합니다.

```python
sum([1,2,3,4,5])

# return 0 + 1 + 2 + 3 + 4 + 5
```

'0'또한 문자열과 같은 **클래스의 인스턴스**라는 점을 계속 기억하셨기를 바랍니다.

맨 처음 계산이 시작되는 부분은 '0 + 1'입니다.

다음과 같은 과정이 발생하는 겁니다 

```python
# 첫번째 일어나는 일 (엄밀히 말해서는 아님)
0.__add__(1)

# 두번재 일어나는 일
1.__radd__(0)

# 그리고 반환되는 값
1
```

더하기 부호 왼쪽에 있는 타입은 반드시 \_\_add\_\_(), 오른쪽에 있는 타입은 \_\_radd\_\_()가 정의되어 있어야 합니다.

```python
class Money:
    def __init__(self, money):
        self.m = money
    
    def __add__(self, other):
        return Money(self.m + other.m)
        
    def __radd__(self, other):
        # sum() 함수를 사용할 때, 두번쨰 인자를 제공하지 않은 경우 기본값이 0이기 때문에 이 경우를 포함해야합니다.
        if other == 0:
            return self
        else:
            return self.__add__(other)

mm = [Money(10), Money(20), Money(30)]

MyMoney = sum(mm)

print(MyMoney.m)    # 60
```

------

파이썬 버전 3.8 변화:

*start* 인자는 키워드 인자(keyword argument)로 쓸 수 있습니다.

```python
# 파이썬 버전 3.8 이전
sum([1,2,3,4,5], start = 10)    # TypeError: sum() takes no keyword arguments

# 파이썬 버전 3.8 이후
sum([1,2,3,4,5], start = 10)    # 25
```

## 파이썬 공식 문서 - 영어(원문)

sum(iterable, /, start = 0)

Sums *start* and the items of an *iterable* from left to right and returns the total.<br>
The *iterable's* items are normally numbers, and the start value is not allowed to be a string.

For some use cases, there are good alternatives to sum().<br>
The preferred, fast way to concatenate a sequence of strings is by calling `''.join(sequence)`.<br>
To add floating point values with extended precision, see [math.fsum()](https://docs.python.org/3/library/math.html#math.fsum).<br>
To concatenate a series of iterables, consider using [itertools.chain()](https://docs.python.org/3/library/itertools.html#itertools.chain).

Changed in version 3.8:

The *start* parameter can be specified as a keyword argument.