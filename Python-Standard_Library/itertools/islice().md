# islice

iterable slice의 줄임말 i + slice

이터러블을 슬라이스된 이터러블로 만들어주는 함수.<br>
결과물만 놓고 볼 때, 인덱싱된 것과 다를게 없다.

## 목차

* 설명

<br>

## 설명

`itertools.islice(iterable, stop)`

`itertools.islice(iterable, start, stop[, step])`

이터러블(iterable)인 첫번째 인자 *iterable*로부터 이터러블을 만듭니다.

```python
from itertools import islice

string = '1234567'

assert [x for x in islice(string, 7)] == [x for x in string]
```

0이 아닌 3개 이상의 인자, *iterable*, *start*, *stop*이 주어진 경우,<br>
*iterable*의 *start + 1*번째 요소부터 *stop*번째 요소까지 이터레이트(iterate)합니다.

```python
from itertools import islice

string = '1234567'

for x in islice(string, 3, 7):
    print(x, end = ' ')

# 출력 : 4 5 6 7
```

Afterward, elements are returned consecutively unless step is set higher than one which results in items being skipped. 
If stop is None , then iteration continues until the iterator is exhausted, if at all; otherwise, it stops at the specified position. 

인자가 4개 모두 주어졌을 경우, 마지막에 해당되는 *step*이 **1**인 경우 요소들이 연속적으로 반환됩니다.

범위를 나타내는 `slice(start, end, step)`와 똑같이 작동합니다.

```python
from itertools import islice

data = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

mySlice = slice(0, 10, 3) # 첫 원소부터 3개 간격으로 9까지 인덱스

for x in islice(data, 0, 10, 3):
  print(x, end = ' ')

print()

for x in data[mySlice]:
  print(x, end = ' ')
  
# 출력 : 1 4 7 10 
# 출력 : 1 4 7 10 
```

일반 슬라이싱(예 : `myList[1:10:2]`)과 같이 음수 인덱싱(마지막 인덱스 = -1)을 지원하지 않습니다 

<br>

`islice`의 유사 구현은 아래와 같습니다:

```python
def islice(iterable, *args):

  # islice('ABCDEFG', 2) --> A B 
  # islice('ABCDEFG', 2, 4) --> C D 
  # islice('ABCDEFG', 2, None) --> C D E F G 
  # islice('ABCDEFG', 0, None, 2) --> A C E G 

  s = slice(*args) 
  start, stop, step = s.start or 0, s.stop or sys.maxsize, s.step or 1 
  it = iter(range(start, stop, step)) 

  try:
    nexti = next(it) 
 
  except StopIteration:
    # *start*까지 이터러블 요소를 소모합니다.
    for i, element in zip(range(start), iterable):
      pass
    return 
 
  try:
    for i, element in enumerate(iterable):
      if i == nexti:
        yield element 
    nexti = next(it) 
  except StopIteration:
    # *stop*까지 요소를 소모합니다. 
    for i, element in zip(range(i + 1, stop), iterable):
      pass
```