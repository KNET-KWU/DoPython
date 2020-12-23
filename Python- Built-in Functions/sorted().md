# sorted()

sort - 정렬하다

## 목차

* 파이썬 공식 문서 - 한글(번역)
    
    * 두번째 키워드 인자 **key**
    
    * `__lt__` 정의 안된 타입 정렬하기
    
    * 세번째 인자 *reverse*
    
* 파이썬 공식 문서 - 영어(원문)

## 파이썬 공식 문서 - 한글(번역)

`sorted(iterable, *, key=None, reverse=False)`

첫번째 인자로 주어진 이터러블(*iterable*)을 정렬된 **새로운 리스트**로 반환합니다.

```python
nums = [ 6, 2, 1 , 5, 12, 300]

sortedNums = sorted(nums)

sortedNum   # [1, 2, 5, 6, 12, 300]

# 기존의 리스트는 그대로 있습니다
nums    # [6, 2, 1, 5, 12, 300]
```

<br>

두번째(*key*)와 세번째(*reverse*)인자는 인자로 대입하는 경우, 반드시 키워드 인자로 작성되어야 합니다.

```python
it = ['b', 'c', 'f', 'a']

sorted(it, lambda x,y: x>y, True)   # TypeError: must use keyword argument for key function

sorted(it, key = lambda x,y: x>y,reverse = True)    # ['f', 'c' ,'b', 'a']
```

문자열 또는 숫자로만 구성된 파이썬에 내장된 이터러블 타입의 경우 `key` 인자 없이 정렬할 수 있습니다.

<br>

### 두번째 키워드 인자 **key**

두번째 키워드 인자 *key*는 정렬되는 기준을 의미합니다.

*key* 인자에 아무것도 대입을 안하는 경우, 기본적인 비교 연산자인 '<' (매직메서드:  `__lt__`)가 호출됩니다.<br>
(lt = less than으로 읽으시면 됩니다)

```python
# sorted() 메서드의 두번쨰 인자 기본값이 무엇인지 확인하기 위해서 에러 유도
sorted(['a', 1])    # TypeError: '<' not supported between instances of 'int' and 'str'
```

커스텀 타입으로 구성된 리스트를 통해서 확인해보겠습니다.

```python
class MyData:
    def __init__(self, num):
        self.num = num
        
    def __lt__(self, other):
        return self.num < other.num

    def __repr__(self) :
        return f'MyData : {self.num}'

myList = [MyData(1), MyData(4), MyData(2), MyData(6), MyData(5), MyData(6)]

print(sorted(myList))   # [MyData : 1, MyData : 2, MyData : 4, MyData : 5, MyData : 6, MyData : 6]
```

<br>

### `__lt__` 정의 안된 타입 정렬하기

그럼 `__lt__` 정의 없이 `key` 인자를 통해서 정렬하는 방법을 알아봅시다.

`key` 키워드는 정렬할 기준인 값을 가져올 함수를 말합니다.

`sorted` 메서드가 `memberList`를 순회하면서 `key`에 있는 함수를 적용한 값을 기준으로 정렬합니다

```python
class Member:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def __repr__(self):
        return f'Member - name : {self.name}, age : {self.age}'

memberList = [ 
    Member('Cpo', 980), 
    Member('Pegg', 21), 
    Member('Tim', 33), 
    Member('Halley', 23)
]

# 이름을 기준으로 정렬
print(sorted(memberList, key = lambda x: x.name))

print('-'*10)

# 나이를 기준으로 정렬
print(sorted(memberList, key = lambda x: x.age))
```

결과 :
```python
# 이름순으로 정렬
[
    Member - name : Cpo, age : 980, 
    Member - name : Halley, age : 23, 
    Member - name : Pegg, age : 21, 
    Member - name : Tim, age : 33
]

# 나이순으로 정렬
[
    Member - name : Pegg, age : 21, 
    Member - name : Halley, age : 23, 
    Member - name : Tim, age : 33, 
    Member - name : Cpo, age : 980
]
```

<br>

## 세번째 인자 *reverse*

세번째 인자 *reverse* 는 `boolean` 값으로, `True` 인 경우 역순으로 정렬됩니다.

```python
print(sorted(memberList, key = lambda x: x.age, reverse = True))

'''
[
    Member - name : Cpo, age : 980,
    Member - name : Tim, age : 33, 
    Member - name : Halley, age : 23, 
    Member - name : Pegg, age : 21 
]'''
```
파이썬 공식 문서에 작성된 정렬 글도 읽어보셔도 좋습니다 -  [Sorting HOW TO](https://docs.python.org/3/howto/sorting.html#sortinghowto).

## 파이썬 공식 문서 - 영어(원문)

sorted(iterable, *, key=None, reverse=False)

Return a new sorted list from the items in *iterable*.

Has two optional arguments which must be specified as keyword arguments.

*key* specifies a function of one argument that is used to extract a comparison key from each element in *iterable* (for example, `key=str.lower`).<br>
The default value is `None` (compare the elements directly).

*reverse* is a boolean value.<br>
If set to `True`, then the list elements are sorted as if each comparison were reversed.

Use [functools.cmp_to_key()](https://docs.python.org/3/library/functools.html#functools.cmp_to_key) to convert an old-style `cmp` function to a `key` function.

The built-in [sorted()](https://docs.python.org/3/library/functions.html#sorted) function is guaranteed to be stable.<br>
A sort is stable if it guarantees not to change the relative order of elements that compare equal -- this is helpful for sorting in multiple passes (for example, sort by department, then by salary grade).

For sorting examples and a brief sorting tutorial, see [Sorting HOW TO](https://docs.python.org/3/howto/sorting.html#sortinghowto).