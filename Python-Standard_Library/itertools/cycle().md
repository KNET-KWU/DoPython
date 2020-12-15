# itertools - cycle

cycle - 순환하다

## 목차

* 설명

* 소모 가능한 이터러블도 문제 없습니다 

    * 조심할 점
    
* cycle을 이용한 간단한 아스키(ascii) 로딩 애니메이션 만들기

<br>

## 설명

`itertools.cycle(iterable)`

첫번쨰 인자 `iterable`를 차례대로 **무한히**(indefinitely) 반환하는 제너레이터(generator)를 반환합니다.

첫번째 인자에 제너레이터와 같이 소모(exhaust) 가능한 이터러블이 주어져도, `cycle` 메서드 내부에서 저장을 하기 때문에

한 번 순환 이후에 에러를 던질 걱정은 안하셔도 됩니다.

<br>

## 소모 가능한 이터러블도 문제 없습니다 

제너레이터와 같은 소모 가능한 이터러블은 1회 사용 후에는 None을 반환합니다.

```python
gen10 = ( x for x in range(10) ) # generator comprehension

for x in gen10:
    print(x)

# 아무것도 출력하지 않습니다.
for x in gen10:
    print(x)
```

`cycle`의 경우 문제 없습니다

```python
from itertools import cycle

gen10 = ( x for x in range(10) )

# 무한히 출력합니다
for x in cycle(gen10):
    print(x)
```

아래의 코드는 `cycle` 메서드와 똑같이 작동합니다 (파이썬으로 구현)

보시면 알겠지만, 걱정하신 부분은 염려안해도 됩니다.

```python
def cycle(iterable):
    # cycle('ABCD') --> A B C D A B C D A B C D ... 
    saved = [] 
    
    for element in iterable:
        yield element
        saved.append(element) 
    
    while saved:
        for element in saved:
            yield element
```

<br>

### 조심할 점

`cycle` 메서드는 소모 가능한 이터러블, 즉 제너레이터와 같이 지연 연산(lazy process)으로 작동하지 않기 때문에

`cycle`을 쓰려는 이터러블의 **요소의 크기가 매우 거나 긴** 경우 메모리를 많이 차지할 수 있습니다

<br>

## cycle을 이용한 간단한 아스키(ascii) 로딩 애니메이션 만들기

이건 제가 예전에 GUI 없는 앱을 만들때 다른 프로젝트를 참고해서 만든 기억이 있어서 적은 예시입니다.

```python
import console
import time
from itertools import cycle

loading = ['|', '/', '-', '\\']

while True:
    for x in cycle(loading):
        print(x, end = '')
        time.sleep(0.1)
        console.clear()
```

쓰레딩(Threading)을 이용해서 동시에 다른 작업을 하면서 아스키 로딩 애니메이션을 보여주면 아래와 같습니다

```python
import threading
import console
import time
from itertools import cycle

loading = ['|', '/', '-', '\\']

stop = False
startTime = time.time()

def loadingAnimation():
    global stop
    # cycle 자체가 while-loop 이므로 어색할 순 있지만 정상입니다
    for x in cycle(loading):
        if not stop:
            console.clear()
            print(x, end = '')
            time.sleep(0.1)

def stopFunc():
    t = time.time()
    global stop
    while True:
        if not stop:
            # 3초마다 정지
            if time.time() - t > 3:
                stop = True
        else:
            console.clear()
            for i in range(3, 0, -1):
                print(f'ReStarting in ... {i}')
                time.sleep(1)
                console.clear()
            t = time.time()
            stop = False

stopFuncThread = threading.Thread(target = stopFunc)
loadingAnimationThread = threading.Thread(target = loadingAnimation)

stopFuncThread.start()
loadingAnimationThread.start()

# 시작하고 20초가 지나면 정지
while True:
    if time.time() - startTime > 20:
        loadingAnimationThread.join()
        stopFuncThread.join()
```

사실 이거 보여주려고 `cycle` 글을 쓴게 거의 맞습니다.