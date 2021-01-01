# combinations

combination - 조합(수학에서의 n C m 형태의 조합입니다), 단 중복되는 원소는 알아서 제거해야 합니다

## 목차

* 설명

<br>

## 설명

`combinations(iterable, r)`

Return r length subsequences of elements from the input iterable.

첫번째 인자 `itertable`의 이터러블 요소들을 **자연수인** 두번째 인자 `r`의 길이의 조합으로 구성된 이터러블을 반환합니다.

조합(combination)의 순서는 주어진 `iterable` 요소의 순서에 따라 만들어집니다.

첫번재 인자 `iterable`이 정렬되어 있다면, `combination` 반환 값 또한 정렬된 순서로 반환됩니다.

```python
from itertools import combinations

a = ['A', 'B', 'C', 'D']
b = ['D', 'C', 'B', 'A']

print(list(combinations(a, 2)))
# [('A', 'B'), ('A', 'C'), ('A', 'D'), ('B', 'C'), ('B', 'D'), ('C', 'D')]

print(list(combinations(b, 2)))
# [('D', 'C'), ('D', 'B'), ('D', 'A'), ('C', 'B'), ('C', 'A'), ('B', 'A')]
```

첫번째 인자로 주어지는 `iterable`의 요소들은 값과 상관없이 다른 요소로 취급합니다.

그러므로 수학적 의미로 말하는 *중복된 값이 없는* 조합을 갖고 싶다면 이터러블을 `set` 생성자를 거쳐 대입하면 됩니다.

```python
from itertools import combinations

a = ['A', 'A', 'B', 'D']

print(list(combinations(a, 2)))
# [('A', 'A'), ('A', 'B'), ('A', 'D'), ('A', 'B'), ('A', 'D'), ('B', 'D')]

print(list(combination( set(iterable), r) )
# [('D', 'B'), ('D', 'A'), ('B', 'A')]
```

`combinations`는 다음 코드와 유사하게 작동합니다:

```python
# combinations('ABCD', 2) --> AB AC AD BC BD CD 
# combinations(range(4), 3) --> 012 013 023 123 

def combinations(iterable, r):
    pool = tuple(iterable) 
    n = len(pool) 
    
    if r > n:
        return 
    indices = list(range(r)) 
    yield tuple(pool[i] for i in indices) 
    
    while True:
        for i in reversed(range(r)):
            if indices[i] != i + n - r:
                break
        else:
            return
        indices[i] += 1
        
        for j in range(i+1, r):
            indices[j] = indices[j-1] + 1
        yield tuple(pool[i] for i in indices)
```

`itertools.premutations()`을 이용해서 `combination`을 구현할 수 있습니다.

```python
def combinations(iterable, r):
    pool = tuple(iterable)
    # 반환되는 요소의 길이(개수)는 n 입니다.
    n = len(pool) 
    
    for indices in permutations(range(n), r):
        if sorted(indices) == list(indices):
            yield tuple(pool[i] for i in indices)
```