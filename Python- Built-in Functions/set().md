# set()

set - 집합

## 목차

* 파이썬 공식 문서 - 한글(번역)
    
* 파이썬 공식 문서 - 영어(원문)

## 파이썬 공식 문서 - 한글(번역)

`class set([iterable])`

`set`은 중복되지 않은 요소들로 구성된 집합을 반환합니다.

```python
# 그냥 만드는 방법

nums = [1,1,1,2,2,3,3,4,4,4,5]
mySet = set(nums)

mySet = {1,1,1,2,2,3,3,4,4,4,5}

# 컴프리헨션으로 만들기
mySet = { x for x in nums }
```

`set`은 함수라기보다 타입 오브젝트의 생성자입니다.

## 파이썬 공식 문서 - 영어(원문)

`class set([iterable])`

Return a new [set](https://docs.python.org/3/library/stdtypes.html#set) object, optionally with elements taken from *iterable*.<br>
`set` is a built-in class.<br>
See [set](https://docs.python.org/3/library/stdtypes.html#set) and [Set Types -- set, frozenset](https://docs.python.org/3/library/stdtypes.html#types-set) for documentation about this class.

For other containers see the built-in 
[frozenset](https://docs.python.org/3/library/stdtypes.html#frozenset), 
[list](https://docs.python.org/3/library/stdtypes.html#list), 
[tuple](https://docs.python.org/3/library/stdtypes.html#tuple), and 
[dict](https://docs.python.org/3/library/stdtypes.html#dict) classes, as well as the 
[collections](https://docs.python.org/3/library/collections.html#module-collections) module.