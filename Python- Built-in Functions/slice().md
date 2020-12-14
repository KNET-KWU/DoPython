# slice()

## 목차

* 파이썬 공식 문서 - 한글(번역)
    
* 파이썬 공식 문서 - 영어(원문)

## 파이썬 공식 문서 - 한글(번역)

`class slice(stop)`<br>
`class slice(start, stop[, step])`

주어진 인자(*start*, *stop*, *step*)에 따라 슬라이스 객체(Slice Object)를 반환합니다.

`slice` 객체는 유의미한 데이터가 담겨있는 객체가 아닌 **슬라이스를 할 범위**를 의미합니다.

다시 말해, 리스트(list)와 같은 이터러블을 인덱스에 따라 나눌 지표 같은 것이라고 생각하면 됩니다.

`slice` 메서드에서 반환된 slice 객체는 슬라이스된 구성요소(`start`, `stop` and `step`) 말고는 기능적 요소가 없습니다.<br>

```python
mySlice = slice(0, 20, 4)

print(mySlice)  # slice(0, 20, 4)

mySlice.start # 0
mySlice.stop # 20
mySlice.step # 4
```

Slice objects are also generated when extended indexing syntax is used.<br>

슬라이스 객체는 리스트, 튜플, 세트, 등 이터러블 객체를 슬라이싱할 때 생성됩니다.

```python
myList = [1,2,3,4,5,6,7,8,9,10]

s = slice(0, 10, 2)
myList[s]    # [1, 3, 5, 7, 9]

# 위와 아래는 같습니다.
myList[0:10:2]  # [1, 3, 5, 7, 9]
``` 

슬라이스할 객체의 인덱스를 초과해도 최대 인덱스의 아이템까지 반환합니다.

```python
myList = [1,2,3,4,5,6,7,8,9,10]

s = slice(0, 10000, 2)
myList[s]    # [1, 3, 5, 7, 9]

s = slice(0, 10000, 1)
myList[s]   # [1,2,3,4,5,6,7,8,9,10]
```

그냥 데이터를 담고 있는 클래스 인스턴스에 불구하지만, 수학 용도의 파이썬(Numerical Python)과 다른 모듈에서는 확장을 통해 사용됩니다.

## 파이썬 공식 문서 - 영어(원문)

`class slice(stop)`<br>
`class slice(start, stop[, step])`

Return a [slice](https://docs.python.org/3/glossary.html#term-slice) object representing the set of indices specified by `range(start, stop, step)`.<br>
The *startand* step arguments default to `None`.<br>
Slice objects have read-only data attributes `start`, `stop` and `step` which merely return the argument values (or their default).<br>
They have no other explicit functionality; however they are used by Numerical Python and other third party extensions.<br>
Slice objects are also generated when extended indexing syntax is used.<br>
For example: a[start:stop:step] or a[start:stop, i].<br>
See [itertools.islice()](https://docs.python.org/3/library/itertools.html#itertools.islice) for an alternate version that returns an iterator.