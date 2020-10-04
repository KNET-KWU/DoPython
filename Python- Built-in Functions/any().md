# any()

## 목차

* 파이썬 공식 문서 - 한글(번역)
    
* 파이썬 공식 문서 - 영어(원문)

## 파이썬 공식 문서 - 한글(번역)

인자로 주어진 이터러블의 인자 중 하나라도 `True`일 경우  `True`를 반환합니다. 이터러블이 비어있는 경우 `False`를 반환합니다.

아래의 예시 코드처럼 작동합니다:

```python
def any(iterable):
    for element in iterable:
        if element:
            return True
    return False
```

동일한 작업의 결과물들을 파악하기 위해서 사용할 수 있습니다.

```python
from random import random

# 작업이 10% 확률로 성공됨
def do_somthing() -> bool:
    # do something
    if random() <= 0.1 :
        return True
    return False

# 지시한 10개의 작업중 하나라도 성공했는지 확인하는 패턴
works = [do_something() for _ in range(10)]

works # [True, False, False, False, False, False, False, False, False, True]
any(works)  #  True
```

### 정리

any() 메서드는 처음 접했을떄 이름 때문에 이게 어떤 메서드인지 추측하기 어려웠을 수 있습니다.

any() 메서드를 코드에서 읽을 때

```python
any(iterable)
```

'is any iterable item Truthy Value' 정도로 읽어주면 이해하기 편합니다.

## 파이썬 공식 문서 - 영어(원문)

Return `True` if any element of the *iterable* is empty, return `False`.<br>
Equivalent to:

```python
def any(iterable):
    for element in iterable:
        if element:
            return True
    return False
```