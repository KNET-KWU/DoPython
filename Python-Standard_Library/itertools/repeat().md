# itertools - repeat

repeat - 반복하다

## 목차

* 설명

* 하나의 객체말고 여러개의 객체가 순환 반복해야하는 경우

<br>

### 설명

`itertools. repeat(object[, times])`

첫번째 인자를 주어진 횟수만큼 반복해서 반환합니다.

횟수(*times*)를 의미하는 두번째 인자가 주어지지 않는 경우 무한히 반복됩니다.

```python
# repeat(10, 3) --> 10 10 10

for x in repeat(9, 3):
    print(x)
# 3 3 3
```

**하나의 객체**를 반복적으로 써야하는(iter) 경우, 그러나 그 횟수를 모르는 경우에 `repeat`를 사용합니다.

```python
from itertools import repeat

# 1. 같은 숫자를 개수 상관없이 반복해야하는 경우
list(map(pow, range(10), repeat(2))) # --> [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]

# repeat를 안쓰는 경우
list(map(pow, range(10), [2 for _ in range(10)]))
```

일반적인 `repeat` 사용 방법은 일정한 값을 반복적으로 `map()`과 `zip()`함수에 공급하는 용도입니다.

<br>

아래 코드와 같이 작동합니다.

```python
def repeat(object, times=None):
    if times is None:
        while True:
            yield object
    else:
        for i in range(times):
            yield object
```

### 하나의 객체말고 여러개의 객체가 순환 반복해야하는 경우

`repeat([1,2,3])`과 같이 작성해서 `1, 2, 3, 1, 2, 3, .... `과 같은 효과를 기대하신 분은 

`itertools`의 `cycle` 메서드를 보시면 됩니다.