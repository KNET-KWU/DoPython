# all()

## 목차

* 파이썬 공식 문서 - 한글(번역)

    * 정리
    
* 파이썬 공식 문서 - 영어(원문)

## 파이썬 공식 문서 - 한글(번역)

all(iterable)

인자로 주어진 이터러블 인자의 모든 요소가 `True`일 경우 `True`를 반환합니다.

아래의 예시 코드처럼 작동합니다:

```python
def all(iterable):
    for element in iterable:
        if not element:
            return False
    return True
```

동일한 작업의 결과물들을 파악하기 위해서 사용할 수 있습니다.

```python
from random import random

# 작업이 90% 확률로 성공됨
def do_somthing() -> bool:
    # do something
    if random() <= 0.9 :
        return True
    return False

# 지시한 10개의 작업이 모두 성공했는지 확인하는 패턴
works = [do_something() for _ in range(10)]

works # [True, True, True, True, True, True, True, True, False, True]
all(works)  #  False
```

### 정리

all() 메서드는 처음 접했을떄 이름 때문에 이게 어떤 메서드인지 추측하기 어려웠을 수 있습니다.

all() 메서드를 코드에서 읽을 때

```python
all(iterable)
```

'is iterable all Truthy Value' 정도로 읽어주면 이해하기 편합니다.

## 파이썬 공식 문서 - 영어(원문)

all(iterable)

Return `True` if all elements of the *iterable* are true (or if the iterable is empty).<br>
Equivalent to:

```python
def all(iterable):
    for element in iterable:
        if not element:
            return False
    return True
```