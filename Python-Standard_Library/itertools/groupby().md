# groupby

group by - ~을 기준으로 분류하다

선요약 : 정렬된 이터러블을 인자로 주면, 하나의 고유값(key)과 연속적으로 나온 값들(group)을 이어 보여준다.

## 목차

* 설명

## 설명

`itertools.groupby(iterable, key=None)`

주어진 첫번째 인자(`iterable`)으로부터 **연속된** 키(key)와 그룹(group)으로 구성된 이터레이터를 반환합니다.

여기서 두번째 인자 `key`는 각각의 이터러블 요소의 키 값(key value)을 연산하는 함수입니다.

`key` 인자에 아무런 값이 주어지지 않는 경우, 정체성 함수(identity function) - 내장 함수 `id()`로 대체 됩니다.

```python
from itertools import groupby

data = ['A', 'C', 'A', 'A','B','B']

for key, group in groupby(data):
    print(f'{key}, {list(group)}')
    
''' 출력
A, ['A']
C, ['C']
A, ['A', 'A']
B, ['B', 'B']
''' 
```

밑에 있는 `groupby()`의 코드를 보면 알 수 있겠지만, `groupby()`는 기본적으로 주어진 이터러블이 **정렬된** 상태임을 가정합니다.

그러므로 위 출력 결과처럼 같은 `'A'` 임에도 불구하고 다른 키로 취급합니다.

```python
sortedData = sorted(data)   # 문자열의 기본 정렬순서 : 알파벳 순

for key, group in groupby(data):
    print(f'{key}, {list(group)}')
    
''' 출력
A, ['A', 'A', 'A']
B, ['B', 'B']
C, ['C']
''' 
```

`groupby()` 는 아래의 코드와 같이 작동합니다:

```
class groupby:

    # [k for k, g in groupby('AAAABBBCCDAABBB')] --> A B C D A B 
    # [list(g) for k, g in groupby('AAAABBBCCD')] --> AAAA BBB CC D 

    def __init__(self, iterable, key=None):
        if key is None:
            key = lambda x: x 
        self.keyfunc = key 
        self.it = iter(iterable) 
        self.tgtkey = self.currkey = self.currvalue = object() 

    def __iter__(self):
        return self 

    def __next__(self):
        self.id = object() 
        
        while self.currkey == self.tgtkey:
            self.currvalue = next(self.it) # Exit on StopIteration 
            self.currkey = self.keyfunc(self.currvalue) 
        self.tgtkey = self.currkey 
        return (self.currkey, self._grouper(self.tgtkey, self.id)) 
    
    def _grouper(self, tgtkey, id):
        while self.id is id and self.currkey == tgtkey:
            yield self.currvalue 
        try:
            self.currvalue = next(self.it) 
        except StopIteration:
            return 
        self.currkey = self.keyfunc(self.currvalue)
```