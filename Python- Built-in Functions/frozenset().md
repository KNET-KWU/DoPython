# frozenset()

frozen - freeze의 과거분사

* (자산•물가•임금 따위의) 동결, 붙박아 놓음.

* (제조•판매 따위의) 정지

## 목차

* 파이썬 공식 문서 - 한글(번역)

    * set과 frozenset의 차이
    
* 파이썬 공식 문서 - 영어(원문)

## 파이썬 공식 문서 - 한글(번역)

`class frozenset([iterable])`

새로운 [frozenset](https://docs.python.org/3/library/stdtypes.html#frozenset) 객체를 반환합니다.

인자 없이도 `frozenset`을 만들수 있으며, *이터러블* 인자가 주어지는 경우 이터러블의 요소로 `frozenset`을 만들 수 있습니다.

```python
frozenset() # frozenset()

frozenset([1,2,3,4,5])  # frozenset({1, 2, 3, 4, 5})
```

다른 파이썬 내장 컨테이너(container) 타입으로 `set`, `list`, `tuple`, `dict`가 있습니다.<br>
내장 모듈 [collections](https://docs.python.org/3/library/collections.html#module-collections)에서 다른 컨테이너 타입을 찾아볼 수 있습니다.

### set과 frozenset의 차이

`frozenset`은 `set`과 같이 유일한 요소를 하나만 가지고 있는 특성을 가지고 있습니다.

* 요소를 추가하거나 추가하지 못합니다

```python
nums = [1,1,1,2,2,3,3,4,5]

mySet = set(nums)

mySet.add(1)
mySet.remove(5)

myFrozenset = frozenset(nums)

myFrozenset.add(1)
# AttributeError: 'frozenset' object has no attribute 'add'

myFrozenset.remove(5)
# AttributeError: 'frozenset' object has no attribute 'remove'
```

* hash 값을 가질 수 있습니다

```python
nums = [1,1,1,2,2,3,3,4,5]

mySet = set(nums)
hash(mySet) # TypeError: unhashable type: 'set'

myFrozenset = frozenset(nums)
hash(myFrozenset) # ok
```

객체를 `hash`할 수 있다는 것은 `key` 값으로 가질 수 있다는 의미입니다.

해시 값을 가질 수 있다면 딕셔너리의 키값으로 사용할 수 있습니다.

```python
nums = [1,1,1,2,2,3,3,4,5]

mySet = set(nums)
myDict = { mySet : 1 } # TypeError: unhashable type: 'set'

myFrozenset = frozenset(nums)
myDict2 = { myFrozenset : 1 }  # ok
```

## 파이썬 공식 문서 - 영어(원문)

class frozenset([iterable])

Return a new [frozenset](https://docs.python.org/3/library/stdtypes.html#frozenset) object, optionally with elements taken from *iterable*.<br>
`frozenset` is a built-in class.<br>
See [frozenset](https://docs.python.org/3/library/stdtypes.html#frozenset) and [Set Types -- set, frozenset](https://docs.python.org/3/library/stdtypes.html#types-set) for documentation about this class.

For other containers see the built-in set, list, tuple, and dict classes, as well as the [collections](https://docs.python.org/3/library/collections.html#module-collections) module