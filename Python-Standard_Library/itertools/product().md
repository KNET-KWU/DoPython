# product

product - (수학) 곱

## 목차

* 설명

<br>

## 설명

주어진 이터러블 인자들의 카르테시안 곱을 반환합니다.

`product()`는 제너레이터 표현식 내부에 있는 `for` 루프와 같습니다.

예를 들어 `product(A, B)`와 `((x,y) for x in A for y in B)` 같습니다.

```python
from itertools import product

myList1 = ['A', 'B', 'C', 'D']

myList2 = [1,2,3,4]

for x in product(myList1, myList2):
    print(x)

# 직접 실행해서 비교해보세요

for x in ((x, y) for x in myList1 for y in myList2)):
    print(x)
```

중첩된 루프는 매 이터레이션마다 맨 오른쪽의 요소들을 순환합니다.

이런 패턴은 곧, 사전순으로 튜플을 만들기 때문에 대입하는 이터러블이 정렬되어 있으면 결과 또한 정렬된 상태로됩니다.

```python
myList1 = [3,1,4,2]
myList2 = ['D', 'B','A','C']

[print(x) for x in product(myList1, myList2)]
```

똑같은 이터러블의 곱을 계산하고 싶으면, 키워드 인자 `repeat`에 원하는 곱의 횟수만큼 대입하면 됩니다.

```python
product(A, repeat = 3)

# 위와 같은 의미입니다.
product(A, A, A)
```

This function is roughly equivalent to the following code, except that the actual implementation does not build up intermediate results in memory:

`product()`는 아래의 코드와 유사하게 구현되어 있습니다만, 메모리에 중간 결과를 저장하지는 않습니다.

```python
def product(*args, repeat=1):

    # product('ABCD', 'xy') --> Ax Ay Bx By Cx Cy Dx Dy
    # product(range(2), repeat=3) --> 000 001 010 011 100 101 110 111

    pools = [tuple(pool) for pool in args] * repeat

    result = [[]]

    for pool in pools:
        result = [x+[y] for x in result for y in pool] 
    for prod in result:
        yield tuple(prod)
```

Before product() runs, it completely consumes the input iterables, keeping pools of values in memory to generate the products. Accordingly, it is only useful with finite inputs.

`product()`는 이터러블의 인자를 반복적으로 사용하기 때문에 함수가 실행되자마자 이터러블의 요소들을 소모합니다.<br>
따라서 주어진 인자가 *일반적으로* 무한개의 요소가 존재하지 않는 이터러블이어야 합니다.

(무한히 반복되는 이터러블의 예 : `itertools.cycle`)