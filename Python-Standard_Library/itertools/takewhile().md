# takewhile

~일 때까지 이터레이트 해라

## 목차

* 설명

## 설명

`itertools.takewhile(predicate, iterable)`

함수인 첫번째 인자가 `True`를 반환하는 경우 두번째 인자 `iterable`의 요소를 반환합니다.

```python
from itertools import takewhile

list(takewhile(bool, [1,1,0,1,1,1,1,1,1]))    # [1, 1]
```

`takewhile`는 아래의 코드와 같이 작동합니다.

```python
# takewhile(lambda x: x<5, [1,4,6,4,1]) --> 1 4

def takewhile(predicate, iterable):
    for x in iterable:
        if predicate(x):
            yield x
        else:
            break
```

`itertools.filterfalse`와 달리 주어진 이터러블의 모든 인자를 순회한 뒤 값을 반환하는 형태가 아닙니다.

제너레이터-이터레이터이므로 인자로 주어진 이터러블의 용량이 크거나 `predicate`인자가 시간이 오래 걸려도

`takewhile` 반환값을 호출 할 때마다 실행되므로 사용자측면에서 비동기로 처리할 수 있습니다.

**단** 아래의 코드와 같이 `takewhile` 객체를 한번에 소모하지 않는 경우, 도중에 수정을 하면 의도와 다른 결과를 보일 수 있습니다.

```python
from itertools import takewhile

s = [1,1,1,1,1]

a = takewhile(bool, s)


print(next(a))  # 1
s[1] = 0
print(next(a))  # raise StopIteration
```