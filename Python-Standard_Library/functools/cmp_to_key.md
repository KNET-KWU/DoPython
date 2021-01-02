# cmp_to_key

`cmp_to_key`는 주로 파이썬 2.x 버전에서 파이썬 3.x 버전으로 이전 되면서 사용하는 메서드입니다!

## 목차

* 설명

<br>

## 설명

`functools.cmp_to_key(func)`

Python2 방식처럼 값을 직접 비교해서 큰지 작은지 같은지 여부를 반환하는 메서드가 그대로 Python3에서 쓰일 수 있도록 해주는 래퍼(Wrapper)메서드입니다.

*예시를 직접 보는 것이 더 빠를 수 있습니다*

키(key) 값 기준으로  참조하는 정렬, 비교 메서드에 사용됩니다.<br>
(sorted() , min() , max() , heapq.nlargest() , heapq.nsmallest() , itertools.groupby())

`cmp_to_key`는 유일 한 하나의 인자로 값을 정렬, 비교하는 메서드이며, 두개의 인자를 받고 비교후 작으면 음수 같으면 0 크면 양수를 반환해야 합니다.

**현재**의 정렬은 int와 string 같은 등호 연산자('>', '<')와 같이 대소 구분이 가능한 파이썬 내장 타입을 반환하는 `key` 메서드가 주어야합니다.

-1, 0, 1을 반환해서 정렬하는 예전, 다른 언어와 다르게 이제는 False, True를 반환합니다.

Python 2.x에 쓰인 비교 함수를 Python 3.x을 기준으로 정렬하는 메서드(key)를 반환하는 형태로 바꿔준다고 생각하시면 됩니다.

```python
from functools import cmp_to_key

it = [10, 3, 4, 8, 9]

# 옛날 방식 - 두 개의 인자
lk= sorted(it, key= cmp_to_key(lambda x, y: x-y))

print(list(lk)) # [3, 4, 8, 9, 10]

# 현재 방식 - 한 개의 인자
lk= sorted(it, key= lambda x:x)

print(list(lk)) # [3, 4, 8, 9, 10]
```

예전 방식은 음수, 양수, 0을 반환해야하는 정렬 메서드였고

현재 방식은 key 메서드가 파이썬 내장 데이터 타입(str, int, float)을 통해서 비교 가능하도록 원초적인 데이터를 반환하는 것을 볼 수 있습니다.

가장 간단한 함수 `lambda x : x`를 보면 느낌이 오시나요?