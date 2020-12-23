# starmap

`star` - 우리가 흔히 곱하기, 별 문자라고 생각하는 그 문자 `*` 맞습니다(shift + 8)

파이썬에서는 전개 연산자(unpacking), 집합(?, 전개의 반대) 연산자(packing) 입니다.

`map` - 함수형 프로그래밍의 대표적인 함수 `map` 입니다.

unpacking 연산자(`*`)와 map 함수를 합친 것이 `starmap` 입니다.

<br>

## 목차

* 설명

<br>

## 설명

`itertools.starmap(function, iterable)`

*이터러블*인 두번째 인자 `iterable`를 인자로 받는 *함수*인 첫번째 인자 `function`을 실행한 결과물들로 이루어진 이터러블을 반환합니다.

함수 이름 그대로 `map` 함수와 다를 것이 거의 없지만 튜플로 이미 모여진(packed, zipped) 인자들을 대신 언패킹(unpacking) 해줍니다.

`map()`과 `starmap()`의 차이는 다음과 같습니다.

```python
from itertools import starmap

print(list(starmap(lambda x,y: x+y, [(1,2),(2,3), (3,4)] ))) # [3, 5, 7]

print(list(map(lambda  x : x[0] + x[1], [(1,2),(2,3), (3,4)] ))) # [3, 5, 7]
```

`map`은 1:1 대응하는 함수와 같이 하나의 인자(x 축) 그리고 하나의 값(y축)으로 구성됩니다.

그래서 두개의 인자를 대입해야하는 함수의 경우 `map` 같은 경우 여러 인자가 담긴 하나의 튜플로 받은 후

`x[0] + x[1]` 처럼 인덱싱을 통해 참조해야하기 때문에 원래 두 개 이상의 인자를 받는 함수의 경우 그대로 활용할 수 없습니다.

**반면**

`starmap`은 이터러블 앞에 아스테리크(`*` 또는 별) 연산자가 있어 언패킹(un-packing)이 되기 때문에

두 개 이상의 인자가 있어도 인자에 각각 대입이 됩니다.

`x + y` 처럼 하나의 튜플이 아닌 각각 인자로 사용할 수 있죠.

<br>

단순한 동작을 추가한 `starmap`은 `map` 함수에 인자를 언패킹하는 데코레이터를 한 것과 같습니다.

```python
def star(function):
    
    def starFunc(function, args):
        # 여기서 *(unpacking)한 것과 같습니다
        for arg in args:
            yield function(arg)
    
    return starFunc


# 파이썬 내장함수 map을 재정의할 수 없어 map과 똑같은 myMap을 만들었습니다.
@star
def myMap(function, *args, **kwargs):
     for x in map(function, args):
        yield x

list(myMap(lambda  x: x+10, [1, 2, 3]))    # [11, 12, 13]
```

`starmap`은 아래의 코드와 같이 작동합니다.

```python
def starmap(function, iterable):
    # starmap(pow, [(2,5), (3,2), (10,3)]) --> 32 9 1000 
    for args in iterable:
        yield function(*args)
```