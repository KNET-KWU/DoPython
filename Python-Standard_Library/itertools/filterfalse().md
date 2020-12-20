# filterfalse

False 거르기

## 목차

* 설명

## 설명

```itertools.filterfalse(predicate, iterable)```

첫번째 인자인 `predicate` 함수에서 `False`인 요소로 구성된 이터러블을 반환합니다.

```python
from itertools import filterfalse

class Student:
    # absent - 결석, 부재
    def __init__(self, name, absent = False):
        self.name = name
        self.absent = absent

    def __repr__(self):
        return f'Student : {self.name}'

students = [Student('ccppoo'), Student('KyuKyu', True), Student('Kim') ]

# 결석이 아닌 학생들 출력
for s in filterfalse(lambda x : x.absent, students):
    print(s)
    
# Student : ccppoo Student : Kim
```

첫번째 인자 `predicate`에 `None`이 주어진 경우, 값이 `False`로 판정되는 요소로 구성된 이터러블을 반환합니다.

참값을 판별하는 내장 함수인 `bool` 함수를 인자로 대입하는 것과 같습니다.

```python
from itertools import filterfalse

data = ['', 'hello', [], {}, 0, False, True]

for x in filterfalse(data):
    print(x)
    
# [] {} 0 False
```

아래의 코드와 같이 작동합니다

``` python
# filterfalse(lambda x: x%2, range(10)) --> 0 2 4 6 8

def filterfalse(predicate, iterable):
    if predicate is None:
        predicate = bool

    for x in iterable:
        if not predicate(x):
            yield x
```