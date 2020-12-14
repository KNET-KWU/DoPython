# itertools - count

count - 세다

## 목차

* counter 설명

* counter 예시

* count 추가 정보

## counter 설명

`count(start=0, step=1)`

말 그대로 일정한 숫자 간격만큼 **무한히**  수를 반환하는 이터레이터를 반환합니다.

첫번째 인자는 시작하는 숫자,

두번째 인자는 증가하는 수를 의미합니다.

첫번째, 두번째 인자 모두 기본값이 있으며, 인자가 아무것도 안 주어지면 0부터 시작하는 양의 정수를 차례대로 반환합니다.

```python
from itertools import count

for x in count():
    if x > 100:
        break
    print(x)
    
# 0, 1, ... ,100

# count(10) --> 10 11 12 13 14 ...
# count(2.5, 0.5) -> 2.5 3.0 3.5 ...
```

<br>

## counter 예시

(1) `count` 메서드는 `zip()`과 함께 이용되어 일렬번호를 붙이기 위해서 사용될 수도 있고

```python
item = ['sword', 'shield', 'hat', 'knife']

for n, it in zip(count(), item):
    print(f'{n}. {it}')
    
# enumerate를 사용해도 됩니다
for n, it in enumerate(item, 1):
    print(f'{n}. {it}')
    
# 출력 -> 1. sword 2. shield 3. hat 4. knife
```

(2) `count` 메서드는 map을 이용해서 연속된 데이터를 만들수 있습니다.

```python
from itertools import count

def processData(n, data):
    # 실전은 이것보단 복잡하겠죠?
    return n + data
    

data = [1,3,6,9,13]

# data의 개수만큼 차례대로 10, 20, 30, ... 을 반환하는 count
processed = map(processData, count(10, 10), data)

for x in processed:
    print(x)
# 출력 -> 11, 23, 36, 49, 63
```

<br>

## count 추가 정보

`count` 메서드는 아래의 파이썬 코드와 같이 작동합니다.

```python
def count(start=0, step=1):
    n = start
    while True:
        yield n
        n += step
```

<br>

### 두번쨰 인자가 정수가 아닌 경우(부동소수점)

`count(start=0, step=1)` 두번째 인자가 부동소수점일 경우,

count를 간접적으로 이용하는 것이 정확성에 있어서 더 좋습니다.

```python
# 곱하는 경우
0.3 * 10
# 3.0

# 직접 더하는 경우
0.3 + 0.3 +0.3 +0.3 +0.3 +0.3 +0.3 +0.3 +0.3 +0.3 
# 2.9999999999999996
```

그래서 부동소수점을 두번째에 넣는 경우 아래와 같이 사용하는 것이 정확성에 있어서 좋습니다.

```python
from itertools import count

test1 = count(1.0, 0.3)

# 응용
test2 = (1.0 + 0.3 * i for i in count())    # 제너레이터 컴프리헨션

print('~~~ count(1.0, 0.3) ~~~')

for n, x in enumerate(test1):
    if n > 10:
        break
    print(x)
print()
print('~~~ (1.0 + 0.3 * i for i in count()) ~~~')
for n, x in enumerate(test2):
    if n > 10:
        break
    print(x)
```

출력 결과

```python
~~~ count(1.0, 0.3) ~~~
.0
1.3
1.6
1.9000000000000001
2.2
2.5
2.8
3.0999999999999996
3.3999999999999995
3.6999999999999993
3.999999999999999

~~~ (1.0 + 0.3 * i for i in count()) ~~~
1.0
1.3
1.6
1.9
2.2
2.5
2.8
3.1
3.4
3.6999999999999997
4.0
```

When counting with floating point numbers, better accuracy can sometimes be achieved by substituting multiplicative code such as: (start + step * i for i in count()).
