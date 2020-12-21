# tee

기능을 이해하기 전에 함수의 이름 자체에 대해서 생각해봅시다.

`tee`라고 하면 골프의 티샷에 쓰이는 티가 먼저 떠오는데, 사실은 리눅스의 `tee` 명령어를 차용해서 만들어진 것으로 추측됩니다.

리눅스의 `tee` 명령어는 하나의 입력을 받고(stdin) 동시에 표준 출력(stdout)과 파일에 쓰는 명령어 입니다.

파이썬 입장에서 생각해보면 하나를 받고 같은 값을 2 개의 명령어로 각각 다른 함수 보낸다고 이해할 수 있겠네요.

`itertools.tee`는 하나의 이터러블을 받아, 같은 내용을 담은 이터러블 복사본을 새로 만들어 반환하는 함수입니다.

## 목차

* 설명

* tee를 사용할 때 조심해야할 점

    * threadsafe

## 설명

`itertools.tee(iterable, n=2)`

첫번째 인자인 `iterable`가 복사된 두번째 인자 `n`개 만큼의 이터러블을 반환합니다.

간단히 말하면 같은 개수의 이터러블을 복사해 만드는 함수입니다.

```python
from itertools import tee
from functools import reduce
from math import sqrt, pow
from operator import add

data = [34,50,60,45,29]

AnalysisData1, AnalysisData2 = tee(data, 2)

Avg = sum(AnalysisData1)/len(data)
Var = reduce(add, map(lambda x : sqrt(pow((x-Avg), 2)), AnalysisData2), 0)/len(data)

print(f'Average : {Avg}, Variance : {Var}')
```

아래에는 파이썬 코드로 작성된 `tee` 함수의 동작을 비슷하게 구현한 것입니다.

```pyhton
def tee(iterable, n=2):
    it = iter(iterable)
    
    # 1. iterable 인자의 요소를 담을 n개의 큐를 만듭니다.
    deques = [collections.deque() for i in range(n)]
    
    # 2. tee의 반환값을 받을 제너레이터를 만든 형태입니다
    def gen(mydeque):
        while True:
            if not mydeque:             # 3. mydeque가 비어있는 경우
                try:
                    newval = next(it)   # 4. 복사할 이터러블의 다음 요소를 가져옵니다
                except StopIteration:
                    return              # 5. 길이가 0인 이터러블 일 수도 있으므로 예외처리를 합니다. 
                for d in deques:        # 6. 복사본을, 정확히는 iterable의 복사본인 it의 참조값을 저장합니다
                    d.append(newval)
            yield mydeque.popleft()
    
    # 
    return tuple(gen(d) for d in deques)
```

코드를 보면 `tee` 함수 내부에 `gen` 함수를 정의해서 제너레이터를 만든 것을 볼 수 있습니다.

또한 `gen` 함수 스코프(scope) 밖의 `it`를 참조하는 클로저 형태를 띄고 있습니다.

일반적인 이터레이터 다를 점은 없습니다.

## tee를 사용할 때 조심해야할 점

`itertools`의 대부분의 함수는 사용시에 유의할 점이나 조심해야하는 점이 거의 없습니다.

파이썬으로 구현이 된 것은 대부분 우리가 만드는 함수와 다를바가 없기 때문에 문제가 될 가능성은 적지만,

C언어로 구현된(CPython) 함수들의 경우 문제가 생길 수 있습니다.

(1). 첫번째 인자에 어떤 이터러블을 넣는가

(2). CPython과 GIL

### 복사할 원본은 잠시 사용하지 말아줘

#### 첫번째 인자(원본)가 소모가능한 경우

값이 소모되는(exhaust) 이터러블의 경우 `tee`로 만들어진 이터러블을 사용하기 전에 사용되면 안됩니다.

`tee` 함수가 반환하는 이터러블은 제너레이터와 같이 아직 값의 평가(evaluation)가 안된 상태입니다.

즉 실행하기 전까지는 아무것도 아닌, 단지 참조가 있는 상태와 같습니다.

그래서 `tee` 함수의 첫번째 인자 `iterable`는 반환된 이터러블을 사용하기 전까지 소모(exhuast)되어선 안됩니다.

```python
from itertools import tee

data = (x for x in range(10)) # 제너레이터 컴프리헨션

d1, d2 = tee(data, 2)

# data가 먼저 소모되는 경우
[ _ for _ in data]

print(list(d1)) # [] 아무것도 없습니다

print(list(d2)) # [] 여기도 아무것도 없습니다
```

`tee`에서 반환되는 이터러블들은 최초 시행시 값이 복사되기 때문에 

반환된 하나의 이터러블을 사용후 원본을 소모시키면 문제가 되지 않습니다.

```python
print(list(d1)) # [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

[ _ for _ in data]  # 원본이 소모되기전에 d1만 불렀으니깐 d2는 아무것도 없을 줄 알았는데

print(list(d2)) # [0, 1, 2, 3, 4, 5, 6, 7, 8, 9] 정상적으로 나옵니다
```

이해가 안되시면 `tee`의 파이썬 구현코드(위)의 (1.)과  (2.) 줄을 보시길 바랍니다.

#### 첫번째 인자(원본)를 수정하는 경우

앞서 말한 대로 `tee`가 반환한 이터러블은 값이 평가되지 않은 상태입니다.

그래서 **소진**되어서도 안되고 **수정**되어서도 안됩니다.

```python
data = [1,2,3,4,5]

d1, d2 = tee(data, 2)

# 수정을 하는 경우
data[2] = 'asdasd'

print(list(d1)) # [1, 2, 'asdasd', 4, 5]

print(list(d2)) # [1, 2, 'asdasd', 4, 5]
```

원본이 수정되기 전 *단 하나*의 반환된 이터러블이 사용되기만 하면 됩니다.

```python
data = [1,2,3,4,5]

d1, d2 = tee(data, 2)

print(list(d1)) # [1, 2, 3, 4, 5]

data[2] = 'asdasd'
print(list(d2)) # [1, 2, 3, 4, 5]
```

### CPython과 GIL

첫번째 인자 `iterable`이 threadsafe인 객체라도 `tee` 이터레이터(반환값)는 threadsafe 하지 않습니다.

**우선** 파이썬은 GIL이 존재합니다. 곧바로 언급할 C와 달리 한번에 하나의 바이트 코드가 실행되도록 강제합니다.

한번에 한 동작을 수행하므로 파이썬은 다른 언어와 달리 동시성 문제(concurency)라던가 Race Condition과 같은 문제에서 자유로웠습니다.

하지만 순수 C로 구현된 CPython으로 내장된 라이브러리의 경우 GIL과 충돌 할 수 있기 때문에 동시성 문제에서 자유롭지 않습니다.

자세한 내용은 답변을 참조해주세요 [stackoverflow - is the result of itertools.tee() thread-safe](https://stackoverflow.com/questions/6703594/is-the-result-of-itertools-tee-thread-safe-python)

## 정리

`tee` 함수는 반환하는 이터러블 또한 값을 각각 저장하는 구조이기 때문에 원본의 크기가 큰 경우 메모리에 부담이 될 수 있습니다.

일반적으로 복사된 데이터를 동시에 쓰는 경우가 아닌 경우, 새로 복사된 리스트를 만든 후 이터레이터에서 참조하는 것이 더 빠릅니다.
