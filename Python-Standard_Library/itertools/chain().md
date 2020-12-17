# chain

chain - 연쇄, 사슬

## 목차

* 설명

<br>

## 설명

`itertools.chain(*iterables)` 

첫번째 인자 `*iterables`에 앞에 있는 아스테릭(asterik) 또는 '별' 기호는 **1개 이상의 인자**를 튜플로 받는 다는 의미입니다.

여러개의 이터러블(iterable) 인자를 받아서 차례대로, 메서드 이름 처럼 **연쇄(chain)**적으로 줄줄이 요소를 모두 반환합니다.

여러개의 이터러블을 하나의 이터러블처럼 사용하고 싶을 때 사용하면 됩니다.

```python
Member1 = ['ccppoo', 'nyom', 'comput', 'mango']
Member2 = ['hpsmile', 'ddor', 'bok2', 'lemon']

for member in chain(Member1, Member2):
    print( member)
```

chain 메서드는 아래의 코드와 같이 작동합니다

```python
# chain('ABC', 'DEF') --> A B C D E F 

def chain(*iterables):    
    for it in iterables:
        for element in it:
            yield element
```
<br>

### 이터러블 안에 이터러블을 순회하고 싶은데?

이터러블이 담긴 이터러블'들'을 순회하고 싶은 상황이 있을겁니다.

단순히 아스테리스크(Asterisk), 또는 별 `*` 연산자를 통해서 언패킹(un-packing)을 이용해 이터러블의 요소를 전개할 수 있습니다.

```python
from itertools import chain

li1 = ['a', 'b', 'c']
li2 = ['z', 'x', 'y']

chars = [li1, li2]

for char in chain(chars):;
    print(char)
# --> ['a', 'b', 'c'] ['z', 'x', 'y']

for char in chain(*chars):
    print(char)
# --> a b c z x y
```

위와 같이 chain의 인자에 아스테리스크 `*` 기호를 붙이기 싫다면 `chain.from_iterable`를 사용하시면 됩니다.

```python
for char in chain.from_iterable(chars):;
    print(char)
# --> a b c z x y
```

다만 리스트 안에 리스트, 이터러블안에 이터러블 요소 (2단계 깊이)까지 보여집니다.

```python
for char in chain.from_iterable(chars):;
    print(char)
# --> a b c z x y
```
