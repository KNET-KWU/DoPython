# zip_longest

제일 긴거까지 `zip` 해주기

## 목차

* 설명

* 이터러블 인자 중에 길이가 무한인 경우
<br>

## 설명

`itertools.zip_longest(*iterables, fillvalue=None)`

이 함수는 내장 함수인 `zip`과 같은 기능을 가지되, 길이가 다른 인자가 주어졌을 때 다르게 작동합니다.

우선 `zip`과 `zip_longest`에 대해서 간단히 비교하겠습니다.


```python
from itertools import zip_longest

data1 = ['a','b','c']

data2 = [1, 2, 3, 4, 5, 6]

list(zip(data1, data2)) # [('a', 1), ('b', 2), ('c', 3), ('d', 4), ('e', 5)]

list(zip_longest(data1, data2)) # [('a', 1), ('b', 2), ('c', 3), (None, 4), (None, 5), (None, 6)]
```

`zip`은 인자로 주어진 길이가 제일 짧은 `data1`의 길이만큼 zip 한 튜플 리스트를 반환합니다.

`zip_longest`는 인자로 주어진 길이가 제일 긴 `data2`의 길이만큼 zip 한 튜플 리스트를 반환합니다.

<br>

`zip_longest`의 키워드 인자 `fillvalue`에 값이 주어지면 길이가 짧아 `None`으로 채워진 튜플이 `fillvalue`로 대체됩니다.

```python
list(zip_longest(data1, data2, fillvalue = 'XXX'))

# [('a', 1), ('b', 2), ('c', 3), ('XXX', 4), ('XXX', 5), ('XXX', 6)]
```

아래의 코드와 같이 작동합니다.

```python
# zip_longest('ABCD', 'xy', fillvalue='-') --> Ax By C- D-

def zip_longest(*args, fillvalue=None):

    iterators = [iter(it) for it in args] 
    num_active = len(iterators) 
    
    if not num_active:
        return 
        
    while True:
        values = []
        
        for i, it in enumerate(iterators):
            try:
                value = next(it)
            except StopIteration:
                num_active -= 1                 
                if not num_active:
                    return 
                iterators[i] = repeat(fillvalue) 
                value = fillvalue

            values.append(value)

        yield tuple(values)
```

## 이터러블 인자 중에 길이가 무한인 경우

`zip`, `zip_longest`의 인자로 길이가 무한한 이터러블이 주어지는 경우도 있을겁니다.

그런 경우 이터러블의 길이를 제한하는 itertools의 `islice()` 또는 `takewhile()`를 사용하세요.

안그러면 파이썬 터집니다

```python
from itertools import zip_longest, cycle

data1 = ['a','b','c']

# 무한한 이터러블인 itertools.cycle
print(list(zip_longest(data1, cycle([1,2,3]))))
```

실행하면 메모리 때문에 터질겁니다