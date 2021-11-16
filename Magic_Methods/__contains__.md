# __contains__, in 연산자

제가 파이썬을 좋아하는 이유는 `in` 연산자가 있다는 것입니다.

`A in B` 연산자는 뜻 그대로 `A`가 `B`에 있냐고 물어보는 것입니다.

<br>

## 목차

* in 연산자 구현하기

* for ... in 형태는?

<br>

## in 연산자 구현하기

`in` 연산자는 파이썬 사용자에게 있어 '~가 ~에 포함되어 있느냐'라고 묻는 연산자입니다.

직관적인 것임과 동시에 *직관*이라는 것 자체가 추상화가 있음을 의미하기 때문에 `in` 연산자를 

사용자 정의 타입에서 새롭게 정의 할 경우 어느정도 **예측 가능한 결과값을 반환해야 하는** 의무가 있습니다.

<br>

우선 파이썬의 기본 타입에 어떻게 쓰이는지 보겠습니다.

리스트(list), 튜플(tuple), 딕셔너리(dict), 세드(set), 문자열(str)와 같은 시퀀스(sequence) 내장 객체에 정의되어 있으며

사용자가 만들수 있는 타입(클래스) 또한 간접적으로 이용하여 사용할 수 있습니다.

```python
myList = [1,2,3,4, 'ccppoo']
mySet = (1,2, 'ccppoo', 3, 4)
myDict = {'ccppoo' : 12}
myTuple = {1,2,3,'ccppoo', 4}

'ccppoo' in myList  # True
'ccppoo' in mySet   # True
'ccppoo' in myDict  # True
'ccppoo' in myTuple     # True
'pp' in 'ccppoo'     # True
```

<br>

사용자 정의 타입에서 어떻게 구현하는지 보겠습니다.

```python
class Hotel :
    def __init__(self, guestList : list = []):
        self.guestList = guestList

    def __contains__(self, other) -> bool:
        if not type(other) is str:
            return False
            
        return other in self.guestList

tHotel = Hotel(['ccppoo', 'Sara', 'Hally'])

# 문자열 'ccppoo'가 __contains__의 두번째 인자 'other'로 사용됩니다.
'ccppoo' in tHotel  # True
```

`__contains__`의 구현한 부분을 보면 눈치챌 수 있을 수 있겠지만,

`in`을 최종적으로 판단하는 타입(클래스)는 `in` 연산자가 구현된 내장 타입입니다.

무언가를 '담는' 객체에 무언가(other)가 있는지 확인하기 위해서 결국은 '담는'  객체의 `in`연산자를 쓰기 때문이죠.

꼭 `in`이 정의된 내장 타입을 이용할 필요 없이 `Bool` 타입만 반환하는 조건문을 작성하셔도 상관없습니다.

<br>

## for ... in ... 형태는?

파이썬의 `for` 문은 반복문을 순회할 때 아이템을 가져올 이터러블(iterable) `in` 오른쪽에 쓰는데요

이 경우 객체의 `__iter__` 메서드를 호출하여 이터러블을 반환하게 합니다.

```python
class Hotel :
    def __init__(self, guestList : list = []):
        self.guestList = guestList

    def __contains__(self, other) -> bool:
        raise Exception('Something Wrong')
        
    def __iter__(self):
        for it in iter(self.guestList):
            yield it
            
tHotel = Hotel(['ccppoo', 'Sara', 'Hally'])

for x in tHotel:
    print(x)

# ccppoo, Sara, Hally    
```

`for`문의 `in`은 연산자 `in`과 기능은 다르지만, 직관적인 표현 때문에 쓴다는 것을 알아두세요
