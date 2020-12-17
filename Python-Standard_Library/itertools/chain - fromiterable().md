# chain.fromiterable

`itertools`의 `chain` 클래스의 `classmethod`입니다.

## 목차

* 설명

## 설명

`chain.from_iterable(iterable)`

그냥 `chain`은 iterable 내부에 있는 요소들을 순회하는 대신

`from_iterable`는 iterable 내부에 있는 iterable 요소를 순회합니다.

```python
chars = ['ABC', 'DEF']

for char in chain(chars):
    print(char, end = ' ')
# --> ABC DEF
    
print()

for char in chain.from_iterable(chars):;
    print(char, end = ' ')
# --> A B C D E F
```

`from_iterable`는 아래의 코드와 같이 작동합니다.

```python
# chain.from_iterable(['ABC', 'DEF']) --> A B C D E F

def from_iterable(iterables):
    for it in iterables:
        for element in it:
            yield element
```