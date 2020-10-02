# filter()

## 목차

* 파이썬 공식 문서 - 한글(번역)
    
    * filter False
    
* 파이썬 공식 문서 - 영어(원문)

## 파이썬 공식 문서 - 한글(번역)

filter(function, iterable)

filter 함수에 주어진 첫번째 인자 *function*의 인자로 쓰일 두번째 인자 *iterable*로 이터레이터를 만듭니다.

두번째 인자 *iterable*는 연속된 데이터의 형태(sequence), 이터레이션을 지원하는 컨테이너, 또는 이터레이터가 될 수 있습니다.

```python
# filter를 이용해서 영어 대문자만 모으기
filteredString = list(filter(lambda x: x > 'A' and x < 'Z', 'asCswCcqPpPoOoO'))
print(''.join(filteredString))  # CCPPOO
```

If *function* is `None`, the identity function is assumed, that is, all elements of *iterable* that are false are removed.

*function*인자에 `None`을 명시적으로 대입하면, 정체성 함수로 대체됩니다.

이터러블한 객체의 요소들 그자체를 반환하는 함수가 쓰이며, Falsey한 값을 제외한 나머지 요소들을 반환합니다.

```python
myList = [0,1,None, True, False]

filter(myList)
# TypeError: filter expected 2 arguments, got 1

list(filter(None, myList)) # [1, True]

# None을 대입했을 경우 다음과 같습니다

def identity(x):
    return x

# 둘이 같습니다
filter(identity, myList)
filter(lambda x:x, myList)
```

종합해보면 filter 함수는 다음과 같이 유사하게 작동합니다

```python
def filter(function, iterable):
    
    if function is None:
        function = lambda x : x
        
    for item in iterable:
        if function(item):
            yield item
```

한줄로 제너레이터 컴피리헨션(Generator Comprehension)으로 나타내면 아래와 같습니다.

```python
# function이 None이 아닐경우
(item for item in iterable if function(item))

# function이 None일 경우 - 자기 자신을 반환하는 함수로 대체되므로 아래의 표현이 맞습니다
(item for item in iterable if item)
```

### filter False

filter 함수와 반대로 False일 경우를 남겨야하는 경우 [itertools.filterfalse()](https://docs.python.org/3/library/itertools.html#itertools.filterfalse)를 참고하세요.

위의 제너레이터 컴프리헨션을 이용한 filter을 보셨으니 직접 만들어서 사용할수 있습니다

```python
# function이 None이 아닐경우
(item for item in iterable if not function(item))

# function이 None일 경우
(item for item in iterable if not item)
```

## 파이썬 공식 문서 - 영어(원문)

filter(function, iterable)

Construct an iterator from those elements of *iterable* for which *function* returns true.<br>
*iterable* may be either a sequence, a container which supports iteration, or an iterator.<br>
If *function* is `None`, the identity function is assumed, that is, all elements of *iterable* that are false are removed.

Note that `filter(function, iterable)` is equivalent to the generator expression `(item for item in iterable if function(item))` if function is not `None` and `(item for item in iterable if item)` if function is `None`.

See [itertools.filterfalse()](https://docs.python.org/3/library/itertools.html#itertools.filterfalse) for the complementary function that returns elements of iterable for which function returns false.