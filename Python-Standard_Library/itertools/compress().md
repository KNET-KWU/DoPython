# compress

compress - 압축하다

<br>

## 목차

* 설명

<br>

## 설명

`compress(data, selectors)`

이터러블인 첫번째 인자 `data`와 이터러블인 두번째 인자 `selectors`를 순회하면서

`selectors`에 있는 n 번째 요소의 값이 `True` 인경우 `data`의 요소를 담아서 이터레이터로 반환합니다.

```python
from itertools import compress

data = ['Red', 'Yellow', 'Blue', 'Green', 'Brown']
selector = [1, 1, 0, 0, 1]

for x in compress(data, selector):
    print(x)

# Red Yellow Brown
```

두번째 인자 `selectors`에는 `True` 값을 가지는 어떤 요소든 사용할 수 있습니다.

파이썬에서 `False`로 판정되는 값은 `False`, `0`, 비어있는 이터러블(튜플, 딕셔너리, 리스트, 문자열)이 있습니다.

아래와 같이 사용할 수 있습니다.

```python
data = ['Red', 'Yellow', 'Blue', 'Green', 'Brown']
selector = [1, False, '', [], 0]

for x in compress(data, selector):
    print(x)
    
# Red
```

첫번째 인자 `data`, 두번째 인자 `selectors` 두 개의 이터러블 인자 중에서 제일 짧은 인자의 개수만큼 `compress` 메서드가 작동합니다.

`data` 길이가 4, `selectors` 길이가 10인 경우 4개의 `data`만 순회하고

반대로 `data` 길이가 10, `selectors` 길이가 4인 경우에도 4개의 `data`만 순회합니다.

```python
from itertools import compress

data = ['A1', 'A2', 'A3', 'A4', 'A5', 'A6', 'A7', 'A8', 'A9', 'A10']
selectors = [1, 1, 1, 1]

for x in compress(data, selectors):
    print(x, end = ' ')

# A1 A2 A3 A4

print()

data = ['A1', 'A2', 'A3', 'A4']
selectors = [1, 1, 1, 1, 1, 1, 1, 1, 1, 1]

for x in compress(data, selectors):
    print(x, end = ' ')
    
# A1 A2 A3 A4
```

아래의 코드와 유사하게 작동합니다.

```python
# compress('ABCDEF', [1,0,1,0,1,1]) --> A C E F 

def compress(data, selectors):    
    return (d for d, s in zip(data, selectors) if s)
```