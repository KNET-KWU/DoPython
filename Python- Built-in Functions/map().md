# map()

## 목차

* map? 지도? mapping? 지도...???

* 파이썬 공식 문서 - 한글(번역)

    * starmap()
    
    * 어... for 문의 모습이..? (응용)

* 마치며

* 파이썬 공식 문서 - 영어(원문)


## map? 지도? mapping? 지도...???

빅데이터가 큰 주목을 받은 이후로 map-reduce 단어가 심심찮게 보였습니다.

Hadoop map-reduce, tensorflow, numpy, .. 등 함수형 프로그래밍과 함께 많이 언급된 단어였습니다.

설명을 위해서 영어로 된 문서를 찾아보면 *map*과 *mapping*이라는 단어를 보면 처음엔 머리속에는

'지도(map)랑 지도를 한다(mapping)? 이게 무슨 뜻이야?' 하고 혼동스러웠던 경험이 있을겁니다.

*map*의 뜻을 찾아보면 지도, 약도라는 명사라고 나오고

*mapping*의 뜻을 찾아보면 지도 제작, **[수학] 사상, 함수 - 명사**라고 나옵니다.

단순히 유추할 수 있듯이 map()을 의미하는 것은 `1차원 함수에 1:1 대응(매칭)하는 행위`에 해당됩니다.

y = f(x) 라는 간단한 방정식이 있다면

**x**는 처리할, 즉 소모할, 데이터 그리고 **f**는 데이터에 적용시킬 메서드를 의미합니다.

이런 의미에서 프로그래밍에서 *map*이라는 명사가 

##### 여러개의 데이터를 주어진 메서드에 병렬적으로 모두 적용시키는 것

파이썬 세계에서 풀어쓰자면

##### itreable 한 데이터들을 주어진 메서드에 병렬적으로 모두 적용시키는 것

라고 우선 이해를 한 뒤에 접근하는 것이 map()을 이해하는데 필요한 초석입니다.

*map* 이외에 프로그래밍에서는 우리가 흔히 아는 영어 단어 뜻과 다르게 쓰이는 경우가 있어

새로운 개념을 처음 공부할 때 **'이 단어가 내가 아는 단어 뜻과 다르게 쓰이는가?'**하고 찾아보는 것이 도움이 됩니다.


## 파이썬 공식 문서 - 한글(번역)

map(function, iterable, ...)

map 함수의 두번째 인자 이터러블(iterable)의 모든 아이템을 첫번째 인자 함수(function)에 모두 적용한 이터레이터를 반환합니다.

map 함수가 반환하는 'map 객체'(<class 'map'>)는 \_\_iter\_\_() 와 \_\_next\_\_() 를 갖고 있는 이터레이터의 한 종류로<br>
(파이썬 공식 문서 - [iterator 정의](https://docs.python.org/3/glossary.html#term-iterator))

대표적으로 문자열(str), 리스트(list), 딕셔너리(dict)와 같이 이터레이트(iterate)가 가능합니다.

*단!* 차이가 있다면 map 객체는 한번 소진하고 난 뒤 (예시: 코드 1) 다시 사용할 수 없다는 것입니다.

```python
# 직접 실행해보세요!

def addOne(x):
    return x +1

# 두번째 인자는 list comprehension 한 것입니다
print('첫번째 출력\n')

for n, val in enumerate(mappedValue):
    print(f'{n+1}번째 값 : {val}')
    
print('\n두번째 출력')

for n, val in enumerate(mappedValue):
    print(f'{n+1}번째 값 : {val}')
```

map 함수가 반환하는 객체는 한번 소진 후 다시 사용할 수 없는 특징이 제너레이터와 유사합니다.

map의 값을 **재사용**하기 위해서는 다음과 같이 list으로 변환후 다시 사용하는 것을 권장드립니다

```python
# 파이썬 PEP 권장 스타일
mappedValue = [map(addOne, [ x for x in range(10)] )]

# 또는 명시적으로 보이게 하고 싶다면
mappedValue = list(map(addOne, [ x for x in range(10)] ))
```

map 함수에 2개 이상의 이터러블한 인자가 주어진 경우, 

첫번째 인자로 주어진 함수는 이터러블한 인자의 개수만큼 인자를 받고 함수를 적용합니다.

```python
# 직접 실행해보세요!

def addTwoInt(x, a):
    return x + a

mappedValue = list(map(addTwoInt, [ x for x in range(10)] , [ x for x in range(10)]))

for n, val in enumerate(mappedValue):
    print(f'{n+1}번째 값 : {val}')
```

주의할 점으로는 2개 이상의 이터러블한 인자를 대입한 경우 이터러블의 길이가 최소인 이터러블 개수만큼의 길이를 갖는 다는 뜻입니다.

위의 예시는 2,3번 째 이터러블 인자 모두 길이 10의 리스트를 addTwoInt 함수에 대입할 수 있어

리스트의 10개의 요소를 모두 연산하여 10번의 출력을 했습니다.

만약 길이가 다르다면 다음과 같은 결과를 나타냅니다.

```python
def addTwoInt(x, a):
    return x + a

# 길이 10, 5의 이터러블을 인자로 제공
mappedValue = list(map(addTwoInt, [ x for x in range(10)] , [ x for x in range(5)]))

# 1번째 값, ... 5번째 값까지 출력...
for n, val in enumerate(mappedValue):
    print(f'{n+1}번째 값 : {val}')
```

다시 정리하자면,

2개 이상의 이터러블 인자를 제공한 경우, 이터레이터는 가장 짧은 이터러블이 모두 소진(exhausted)될 때 중단합니다.

다시 보는 함수 원형 : `map(function, iterable1, iterable2, ...)`

하나의 요소만 갖고 있는 이터레이터가 아닌 이미 튜플(tuple)로 묶인 이터레이터를 map의 인자로 쓰고자 할 때, [itertools.starmap()](https://docs.python.org/3/library/itertools.html#itertools.starmap)
을 써보세요.


### starmap

starmap의 원형은 다음과 같이 작동합니다(파이썬 공식 문서)

```python
# 제너레이터 객체 반환
def starmap(function, iterable):
    for args in iterable:
        yield function(*args)
```

map과 차이가 있다면 우선 이름에 'star'가 존재한다는 것입니다.

파이썬에서 star(별) 혹은 asterisk(아스테리스크)라고 부르는 문자는 여러 기능을 갖고 있지만,

여기서는 언패킹(unpacking)의 기능으로 쓰인다 - 다른 기능들은 여기서 안 다룬다.

**위의 starmap을 꼭 써야하나?** 라고 물어본다면 아니다 필요하다고 느끼면 그때가서 써도 충분합니다.

```python
def add(*args):
    return sum(args)
    
listofNum1 = [ x for x in range(0,10,1)]
listofNum2 = [ x for x in range(0,20,2)]
listofNum3 = [ x for x in range(0,30,3)]
listofNum4 = [ x for x in range(0,40,4)]
listofNum5 = [ x for x in range(0,50,5)]
```

여러개의 이터러블 인자를 받을 수 있는 add에 더할 5개의 이터러블을 작성했습니다.

starmap은 map의 이터러블 인자가 여러개 있을 경우, 코드가 읽기 힘들다는 점을 보완하기 위해서 만들어진 것으로

가독성의 차이를 중점으로 두고 starmap과 map의 차이를 알아보도록 하세요.


```python
# using map()

mappedVal = map(add, listofNum1, listofNum2, listofNum3, listofNum4, listofNum5)

for n, val in enumerate(mappedVal):
    print(f'{n+1} 번째 값 : {val}')
```

map 함수를 호출할 때 매핑할 함수를 단번에 찾아볼 수 있지만 (`add`)

**몇개의 어떤 이터러블을 매핑할 것인지 한눈에 안들어온다.**

```python
# using starmap()

from itertools import starmap

listsofNum = zip(listofNum1, listofNum2, listofNum3, listofNum4, listofNum5)

starmappedVal = starmap(add, listsofNum)

for n, val in enumerate(starmappedVal):
    print(f'{n+1} 번째 값 : {val}')
```

starmap을 사용하는 경우 사전에 매핑할 변수들을 차례대로 튜플에 묶기 위해서 zip()을 했지만,

map에 비해서 내가 정확히 **어떤 함수와 어떤 이터러블을 매핑하는지** 알아보기 쉽습니다.

### 어... for 문의 모습이..? (응용)

이 문단에서 설명할 내용은 map 함수가 반환하는 map 객체의 특성의 이해를 요구하는 것이기 때문에 걱정안해도 됩니다!

```python
for n, val in enumerate(mappedVal):
    print(f'{n+1} 번째 값 : {val}')
```

사실 이 for문을 일부러 계속 보여줬습니다.

map() 설명을 읽으면서 for문과 유사하게 작동함을 알 수 있습니다.

이 for 문을 map()으로 바꾸는 순서는 다음과 같습니다 (for 에서 map 으로 바꾸는 흐름)

1. 반복적으로 사용하는 함수를 찾는다 -> print

2. 인자로 대입되는 이터러블을 찾는다 -> enumerate(mappedVal)

3. map을 이용한다.

```python
for n, val in enumerate(mappedVal):
    print(f'{n+1} 번째 값 : {val}')

# 즉시 실행 - 1
list(map(print, [f'{n+1} 번째 값 : {val}' for n, val in  enumerate(mappedVal)]))

# for 문을 통해 호출(실행) - 2
mappedPrint = map(print, [f'{n+1} 번째 값 : {val}' for n, val in  enumerate(mappedVal)])
for x in mappedPrint:
    # 출력되는 지점은 for 문의 x에 값이 할당 될 때
    pass
```

우선 위에 map 객체 (<class 'map'>)는 제너레이터와 유사하다고 언급했습니다.

즉, 코드가 map 객체의 데이터를 호출(call)하기 전까지 값이 평가(evaluate)되지 않았다는 것을 의미합니다.

1번 예시의 print와 문자열("n번 째 값 ...")이 매핑된 후 호출하기 직전, 즉 **대기**하고 있는 상태입나다.

그래서 list로 감싸기 전까지 print 함수와 문자열이 **평가되지 않았기 때문에** 함수 포인터와 문자열 포인터만 덩그러니 존재하는 상태입니다.

list로 감싸면 list의 특성상 모든 요소가 평가되므로 예상했던 행동(출력)이 시행되는 것입니다.

2번 예시의 경우 for문 호출을 통해 \_\_next\_\_() 호출이 되면 차례차례 평가되어 문자열이 출력됩니다.

x를 출력할 경우 print 함수는 무조건 None을 반환하기 때문에 `None`이 나옵니다.



## 마치며

map은 주로 이미지의 픽셀 데이터, 대량의 선형 데이터(예: 엑셀)를 병렬로 다룰 때 유용합니다.

*대량*의 데이터를 굳이 언급한 이유는 map이 지연된 평가 방식의 map 객체를 반환하는 것을 보면 알 수 있죠.

파이썬 또는 프로그래밍을 처음 접하시는 분들이라면

"map 대신 그냥 for문 쓰면 안되는거야?" 라고 의문을 제기할 수 있습니다.

물론 가능하지만, map이 병렬적인 연산을 한다는 점에서 for문과 차이가 있을 수밖에 없고

지연된 연산 방식을 사용하는 map이 자원 관리에 있어 for문에 비해 상황에 따라 더 유용할 수밖에 없습니다.

코드 가독성에 있어 한줄로 깔끔하게 표현할 수 있는 장점도 있네요.

지금 당장은 필요성에 공감하지 못할 수도 있어도 알고있기를 바랍니다.

## 파이썬 공식 문서 - 영어(원문)

map(function, iterable, ...)

Return an iterator that applies function to every item of iterable, yielding the results.<br> If additional iterable arguments are passed, function must take that many arguments and is applied to the items from all iterables in parallel.<br> With multiple iterables, the iterator stops when the shortest iterable is exhausted.<br> For cases where the function inputs are already arranged into argument tuples, see itertools.starmap().<br>