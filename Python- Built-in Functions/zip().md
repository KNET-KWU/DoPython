# zip()

## 목차

* zip, 파일 압축?

* 파이썬 공식 문서 - 한글(번역)

   
    * zip을 이용해서 전치행렬만들기 (Transpose Matrix)

    * 길이가 다른 이터러블 인자를 사용해야하는 경우

    * 주의할 점

* 파이썬 공식 문서 - 영어(원문)

## zip, 파일 압축?

데이터 압축을 생각하고 들어왔다면 완전히 다른 것입니다.

[Data Compression and Archiving](https://docs.python.org/3/library/archiving.html)

## 파이썬 공식 문서 - 한글(번역)

인자로 주어진 각각의 이터러블(iterable)의 요소를 집합한 이터레이터(iterator)를 만듭니다.

n번째 튜플은 인자로 주어진 이터러블의 n번째 요소를 갖는 튜플로 구성된 이터레이터를 반환합니다.

```python
string1, string2 = 'ccppoo', 'python'

zipIterator = zip(string1, string2)

print(next(zipIterator)) # 1번째 ('c', 'p')
print(next(zipIterator)) # 2번째 ('c', 'y')
```

인자로 주어진 이터러블 중 가장 짧은 이터러블이 모두 소모되면 이터레이터는 중단됩니다.

```python
num1, num2 = [x for x in range(5)], [x for x in range(3)]

for n, x in enumerate(zip(num1, num2)):
    print(f'{n} 번째 요소 : {x})

# 0 번째 요소 : (0, 0)
# 1 번째 요소 : (1, 1)
# 2 번째 요소 : (2, 2)
```

인자가 하나 밖에 없는 경우 하나의 요소만 있는 튜플로 구성된 이터레이터를 반환합니다.

```python
nums = [ x for x in range(5)]

for x in zip(nums):
    print(x)
    
# (0,), (1,), (2,), (3,), (4,)
```

인자없이 zip 함수를 쓰면 빈 이터레이터를 반환합니다.

```python
for n, x in enumerate(zip()):
    print(f'{n} 번째 요소 : {x}')
    
# 아무것도 출력 안함... ㅋ
```
zip 함수는 다음 코드와 동일하게 작동합니다:

```python
def zip(*iterables):
    # zip('ABCD', 'xy') --> Ax By
    sentinel = object()
    iterators = [iter(it) for it in iterables]
    while iterators:
        result = []
        for it in iterators:
            elem = next(it, sentinel)
            if elem is sentinel:
                return
            result.append(elem)
        yield tuple(result)
```

zip함수는 인자가 주어진 순서를 그대로 각각의 인자 이터러블로부터 하나의 요소가 담긴 튜플을 반환하므로,

n개의 연속된 데이터로 원하는 의도대로 뭉칠수 있습니다.

```python
userNames = ['ccppoo', 'minkyu', 'doyun']
userAges = [3, 7, 31]

class User:
    def __init__(self, name:str, age:int):
        self.name = name
        self.age = age
    
users = []
# userName, userAges 순서로 zip 인자로 넣었으므로 name, age에 각각 언패킹(unpackaged) 됩니다.
for name, age in zip(userNames, userAges):
    users.append( User(name, age))
```

zip 함수는 인자로 주어진 n개의 이터레이터의 길이만큼 한번씩 호출하고,

i번 호출한다면 zip 함수의 i 번째 반환되는 튜플에는 n개의 이터레이터의 i 번째 요소가 있는것을 확인할 수 있습니다.

```python
num1, num2 = [1,2,3,4,5], [10, 20, 30, 40, 50]
ziped = zip(num1, num2)
list(ziped)[0] # (1, 10)
list(ziped)[1] # (2, 20)
```

이러한 효과로 zip을 이용하면 이터러블을 이터러블 개수만큼 분할하는 효과를 볼 수 있습니다.<br>
(원문 : This has the effect of dividing the input into n-length chunks.)

[이터러블한](https://docs.python.org/3/library/stdtypes.html#typeiter) 
기본 자료형 외에 새로 정의한 클래스의 경우, 이터러블의 속성을 갖고 있어야합니다.

이터러블하지 않은 인자가 주어진 경우 TypeError를 던집니다.

zip 함수는 인자로 주어진 이터러블 중 가장 짧은 이터러블의 길이만큼 튜플을 반환하기 때문에, 인자의 길이들이 모두 같지 않은 경우 의도하지 않은 결과를 받을 수 있습니다.

더 정확히 설명을 하자면, 인자로 주어진 n개의 이터러블이 주어지면 n개의 이터러블을 순회하면서 튜플을 만드는데, 최대 길이에 도달하면 (raise StopIteration) zip 함수가 반환을 중단하는 것입니다.

zip 함수의 반환값과 함게 * 연산자(unpacking)를 사용하면 리스트를 처음상태로 다시 만들 수 있습니다.

```python
x = [1, 2, 3]
y = [4, 5, 6]
zipped = zip(x, y)
list(zipped)    # [(1, 4), (2, 5), (3, 6)]

# zip에 3개의 이터러블 인자가 있는 상태: *zip(x,y) == (1,2,3), (4,5,6)
x2, y2 = zip(*zip(x, y))
x == list(x2) and y == list(y2) # True
```

### zip을 이용해서 전치행렬만들기 (Transpose Matrix)

수학을 계속 공부하시는 분이라면 방금 보신 예시에서 흠칫 했을겁니다.

```python
A = [
[1, 2, 3]
[4, 5, 6]
]

AT = list(zip(*A))

print(AT)
#[
#   [1,4],
#   [2,5],
#   [3,6]
#]
```
zip 함수를 이용한다고 바로 되는 것은 아니고

언패킹 연산자(*)를 이용해서 모든 행을 zip 함수의 인자로 넣습니다.

```python
#위와 아래는 같은 상황입니다 ( '*' 연산자 적용시 상황)
AT = list(zip(*A))
AT = list(zip([1,2,3], [4,5,6]))
```

### 길이가 다른 이터러블 인자를 사용해야하는 경우

zip 함수에 이터러블 인자 길이가 다르지만,

다른 이터러블의 길이 상관없이 제일 긴 이터러블 인자를 기준으로 모두 출력하고 싶다면<br>
(이미 소모된 이터러블의 자리에는 None) 

파이썬 내장 라이브러리 itertools.zip_longest()를 사용하면 됩니다.

이름에서 바로 알아볼 수 있겠죠?

```python
from itertools import zip_longest

zipAll = zip_longest([1,2,3], [1,2,3,4,5])

for x in zipAll:
    print(x)
# (1,1)
# (2,2)
# (3,3)
# (None, 4)
# (None, 5)
```

### 주의할 점

zip 함수 실행시 반환하는 객체는 이터레이트(iterate) 후 소모되므로

값을 재사용하고 싶다면 위해서 리스트로 저장해야합니다

```python
zipped = zip([1,2,3,4],[5,6,7,8])

# 첫번째 출력
for x in zipped:
    print(f'item : {x}')

# (1,5), (2,6), (3,7), (4,8)

# 두번째 출력
for x in zipped:
    print(f'item : {x}')
    
# 아무것도 출력 안됨

# 값 재사용, 저장하고 싶다면 list 생성자로 감싸기
zipped = list(zip([1,2,3,4],[5,6,7,8]))
```

## 파이썬 공식 문서 - 영어(원문)

Make an iterator that aggregates elements from each of the iterables.

Returns an iterator of tuples, where the i-th tuple contains the i-th element from each of the argument sequences or iterables. The iterator stops when the shortest input iterable is exhausted. With a single iterable argument, it returns an iterator of 1-tuples. With no arguments, it returns an empty iterator. Equivalent to:

```python
def zip(*iterables):
    # zip('ABCD', 'xy') --> Ax By
    sentinel = object()
    iterators = [iter(it) for it in iterables]
    while iterators:
        result = []
        for it in iterators:
            elem = next(it, sentinel)
            if elem is sentinel:
                return
            result.append(elem)
        yield tuple(result)
```

The left-to-right evaluation order of the iterables is guaranteed. This makes possible an idiom for clustering a data series into n-length groups using zip(\*[iter(s)]\*n). This repeats the same iterator n times so that each output tuple has the result of n calls to the iterator. This has the effect of dividing the input into n-length chunks.

zip() should only be used with unequal length inputs when you don’t care about trailing, unmatched values from the longer iterables. If those values are important, use itertools.zip_longest() instead.

zip() in conjunction with the * operator can be used to unzip a list:

```python
x = [1, 2, 3]
y = [4, 5, 6]
zipped = zip(x, y)
list(zipped)    # [(1, 4), (2, 5), (3, 6)]
x2, y2 = zip(*zip(x, y))
x == list(x2) and y == list(y2) # True
```