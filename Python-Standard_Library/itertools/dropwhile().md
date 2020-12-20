# dropwhile

predicate - 선언하다; 조건이라고 생각하시면 됩니다

## 목차

* 설명

* 주의할 점
<br>

## 설명

`itertools.dropwhile(predicate, iterable)`

첫번째 인자 `predicate` 함수의 인자로 두번째 인자 `iterable`가 소모되고,

처음으로 `predicate` 함수가 `False`를 반환하는 인자부터 요소를 반환합니다. (아래 직접 코드를 봐주세요)

아래의 코드와 같이 작동합니다

```python
# dropwhile(lambda x: x<5, [1,4,6,4,1]) --> 6 4 1

def dropwhile(predicate, iterable):    

    iterable = iter(iterable)

    for x in iterable:
        if not predicate(x):
            yield x
            break

    # 위에서 이미 iterable는 소모되었기 때문에 조건을 만족한 n 번째 요소의 인덱스를 참조할 필요 없이
    # for 문을 순회하면 됩니다

    for x in iterable:
        yield x
```

만약 시간에 관한 데이터가 담긴 이터러블이 있는 상황에

'18:00' 시 이후 데이터를 보고 참고 하고 싶은 경우 이럴수도 있겠습니다.

```python
from itertools import dropwhile

timeData = [{'t' : 16, 'data' : 2}, {'t' : 17, 'data' : 1},  {'t' : 18, 'data' : 10},  {'t' : 19, 'data' : 8} ]

for x in dropwhile(lambda x : x['t'] < 18, timeData):
    print(x) 
    
# {'t': 18, 'data': 10} {'t': 19, 'data': 8}
```

<br>

## 주의할 점

함수인 첫번째 인자 `predicate`가 `False`를 반환할 때까지 두번째 인자 `iterable` 요소 개수만큼 **동기적으로** 실행되어야하는 만큼, 

`iterable` 요소의 크기가 크거나, `predicate` 함수 실행에 많은 연산이 수행되어야 하는 경우 성능에 영향을 끼칠 수 있습니다.

```python
items = [ BigItem1, BigItem2, BigItem3, BigItem4, ... , BigItem999 ]

# dropwhile 객체 내부에서 predicate을 만족하는 인자를 만날 때까지 돌리는 for 문은 동기(sync) 작업임

for x in dropwhile(checkCondition, items):
    # for x in .. 구문 자체를 시행하기 전까지 걸리는 시간이 늘어남 - async for 구문이여도 상관 x
    doSomething(x)
```