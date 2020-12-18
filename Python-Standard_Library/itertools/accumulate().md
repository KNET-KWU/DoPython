# accumulate 

accumulate - 모으다, 축적하다

이 함수는 이름 그 자체에서 값들이 하나로 합쳐지는 *느낌*을 받을 수 있지만,

이터러블을 반환한다는 점을 기억한 상태로 글을 읽어주세요!

## 목차

* 설명

    * 첫번째 인자 `iterable`만 주어진 경우

    * 두번째 인자 `func`가 주어진 경우

    * 세번쨰 인자 `initial`가 주어진 경우 - Python 3.8 추가
    
* 됐고, 마지막 값만 갖고 싶은데?

<br>

## 설명

`itertools.accumulate(iterable[, func, *, initial=None])`

<br>

### 첫번째 인자 `iterable`만 주어진 경우

첫번째 인자 `iterable`의 모든 요소가 합산(+)된 값을 반환하는 이터레이터를 반환합니다.

첫번쨰 인자 `iterable`에 주어진 이터러블을 차례대로 처음부터 누적된 합의 값이 담긴 이터러블을 반환합니다.

```python
from itertools import accumulate

accumulate([1,2,3,4,5]) # 1 3 6 10 15
```

말로 풀어쓰면 "n 번째 요소  = 1, 2, ... ,n 요소의 합" 입니다.

첫번째 인자 `iterable`의 길이(요소 개수)와 같은 수의 길이를 가진 이터러블이 반환됩니다.

`+`, 다르게 표현하자면 `__add__`와 `__radd__`가 정의되어 있는 타입이라면 사용할 수 있습니다.

잘 쓰고 계시는 문자열 타입(str)도 그렇고요

```python
abc = accumulate(['a', 'b', 'c'])

list(abc)   # ['a', 'ab', 'abc']
```

사용자 정의 타입(클래스) 또한 잘 쓸 수 있습니다

```python
class temp:
    def __init__(self , n):
        self.n = n
        
    def __add__(self, other):
        return self.__radd__(other)
        
    def __radd__(self,other):
        return temp(self.n + other.n)
        
    def __repr__(self):
        return f'temp : {self.n}'
        
list(accumulate([temp(x) for x in range(5)]))
# [temp : 0, temp : 1, temp : 3, temp : 6, temp : 10]
```

첫번쨰 인자에는 서로 다른 타입이 이터러블 안에 존재해도 상관없이 `+` 연산이 이루어지기만 하면 됩니다.

<br>

### 두번째 인자 `func`가 주어진 경우

두번째 인자 `func`는  두 개의 인자를 받아들이는 함수이어야 합니다.

첫번쨰 인자 `iterable`만 주어진 경우는 `func` 인자에 `+` 함수를 대입한 것과 같은 것입니다.

```python
from itertools import accumulate
from operator import add, mul

a  = list(accumulate([1,2,3,4,5], add))

a   # [1, 3, 6, 10, 15]

# 더하기 대신 곱하기를 한 경우
accumulate([1,2,3,4,5], mul) # 1 2 6 24 120
```

여러분이 이 글을 읽으시면서 느꼈을 법한 `accumulate`의 *더해서 계속 축척*할 것 같은 느낌이 들어도

꼭 더해야 할 것 같은 기능을 가진 메서드를 인자로 대입할 필요가 없습니다.

다음은 두개의 인자를 받아들인 후 평균값을 반환하는 예시입니다.

```python
def makeAve(val1, val2):
    return (val1+val2)//2
    
values = [10, 3, 23, 1, 12, -20, 4]

list(accumulate(values, makeAve))   # [10, 6, 14, 7, 9, -6, -1]
```

<br>

### 세번쨰 인자 `initial`가 주어진 경우 - Python 3.8 추가

위에 설명한 두 가지 경우에는 첫번째 인자로 주어진 이터러블의 길이만큼 반환되는 이터러블의 길이 또한 같습니다.

하지만, 세 번째 **키워드 인자** `initial`가 주어진 경우 (첫번째 인자 길이) + 1인 이터러블이 반환됩니다.

반환되는 첫번째 이터러블의 요소는 시작값(`initial`)입니다.

```python
accumulate([1,2,3,4,5], initial=100) # 100 101 103 106 110 115

accumulate([1,2,3,4,5], operator.mul, initial=2)   # 2, 2, 4, 12, 48, 240
```

--------

`accumulate` 함수는 아래의 코드와 같이 작동합니다:

```python
def accumulate(iterable, func=operator.add, *, initial=None):
    it = iter(iterable)

    total = initial

    if initial is None:
        try:
            total = next(it)
        except StopIteration:
            return
    yield total

    for element in it:
        total = func(total, element)
        yield total
```

## 마지막 값만 갖고 싶은데?

`accumulate` 함수를 보면 이터러블의 모든 값에 대한 연산이 끝난 값,

즉 누적 순에 따른 **중간 결과에 관심없고 마지막 값**에 관심을 가지시는 분이라면

`itertools.reduce` 함수를 사용하시면 됩니다.

```python
from itertools import accumulate
from functools import reduce
from operator import mul


list(accumulate([1,2,3,4,5], mul))[-1] # 120

reduce(mul, [1,2,3,4,5]) # 120
```