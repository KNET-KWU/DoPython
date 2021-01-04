# partial

partial - (형용사) 일부, 부분적인

메서드에 미리 인자를 넣은 상태로 만들어 주는 메서드

## 목차

* 설명

<br>

## 설명

`functools.partial(func, /, *args, **keywords)`

메서드인 첫번째 인자 `func`가 주어진 두번째 인자 `args`, 세번째 인자 `keywords`가 메서드에 주어진 `partial` 객체가 반환됩니다.

```python
partialFunc = partial(myFunc, data, live = False)

partialFunc()               # 이렇게 메서드를 호출하는 것과
myFunc(data, live = Faslse) # 이렇게 메서드에 인자를 넣어서 호출하는 것과 같습니다.
```

`partial` 메서드로 만든 메서드에 인자를 추가로 넣으면 `partialFunc(age, job)`, 일반 메서드처럼 인자에 추가됩니다.

```python
from functools import partial

def testFunc(*args, **kwargs):
    print(f'args : {args}')     # 키워드 없는 인자들은 튜플로 묶이고
    print(f'kwargs : {kwargs}') # 키워드 있는 인자들은 딕셔너리로 묶입니다

name = 'ccppoo'
age = 99    

partialFunc = partial(testFunc, name, age, happy = True)

partialFunc()
'''
args : ('ccppoo', 99) 
kwargs : {'happy': True}
'''
```

키워드 인자를 추가로 넣는 경우, 미리 넣어둔 키워드 인자(`keywords`) 목록에 추가되며, 같은 *key*를 가진 경우, 덮어써집니다.

```python
# 바로 위 예시코드에 이어서

partialFunc('wear mask', happy = False, live = True)

'''
args : ('ccppoo', 99, 'wear mask')
kwargs : {'happy': False, 'live': True} # 'happy' True -> False로 덮어써졌습니다
'''
```

아래의 파이썬 코드와 같이 작동합니다 :

```python
def partial(func, /, *args, **keywords):

    def newfunc(*fargs, **fkeywords):
        newkeywords = {**keywords, **fkeywords}

        return func(*args, *fargs, **newkeywords) 
    
    newfunc.func = func 
    newfunc.args = args 
    newfunc.keywords = keywords 
    
    return newfunc
```

`partial` 메서드는 첫번째 인자로 주어진 함수와 인자들을 미리 모아 "얼린(fresszes)" 상태로 만듭니다.<br>
어떤 인자를 주어지는 지에 따라 다르게 작동하는 함수를 `partial()`를 이용해 사용하기 더 간단한 상태로 바꿀 수 있습니다.

아래는 정수(int)로 Parse하는 내장 메서드 `int()`입니다.

`partial(int, base =2)` 같이 작성하면 2진수를 정수로 바꿔주는 함수를 만듦과 동시에 알아보기 쉽도록<br>
메서드의 이름을(signature) 직관적으로 만들 수 있습니다(`basetwo`).

```python
from functools import partial
basetwo = partial(int, base=2)
basetwo.__doc__ = 'Convert base 2 string to an int.'
basetwo('10010')    # 18
int('10010', base = 2)  # 이렇게 작성하는 것과 달리 메서드 이름부터 직관적이고 한눈에 알아볼 수 있습니다.
```