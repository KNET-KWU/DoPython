# iter()

sentinel : 

(1) 보초, 파수꾼, 감시인<br>
(2) 컴퓨터 - 구분점 표시<br>

## 목차

* 파이썬 공식 문서 - 한글(번역)
    
    * 두번째 인자 *sentinel*이 **없는** 경우
    
    * 두번째 인자 *sentinel*이 **있는** 경우
    
* 파이썬 공식 문서 - 영어(원문)

## 파이썬 공식 문서 - 한글(번역)

`iter(object[, sentinel])`

첫번째 인자 *object* 내부에 정의된 메서드에 따라서 이터러블한 이터레이터 객체를 만들어 반환합니다.

첫번째 인자 *object*는 두번째 인자 *sentinel*의 존재 유무에 따라 다르게 작동합니다.

### 두번째 인자 *sentinel*이 없는 경우

`iter(object)`

1. 첫번째 인자 *object*는 반드시 [\_\_iter\_\_()](https://docs.python.org/3/reference/datamodel.html#object.__iter__) 메서드가 정의되어야 하는 이터레이션 프로토콜을 준수해야 합니다.

2. 또는 [\_\_getitem\_\_(self, key)](https://docs.python.org/3/reference/datamodel.html#object.__getitem__) 메서드가 정의되어야 하는 시퀀스(sequence) 프로토콜을 준수해야합니다.

위 두가지 조건을 지키지 않는 첫번째 인자가 주어진 경우, `TypeError`를 던집니다.

`__getitem__`이 정의된 객체의 경우 `__getitem__` 메서드가 호출될 때 첫번째 인자(key)로 정수 '0'부터 시작해야됩니다.<br>
`iter()` 메서드가 `__getitem__`을 호출하는 경우 키값을 정수 0부터 조회하기 때문입니다.

```python
class Data:
    def __init__(self):
        self.listData = [ x for x in range(10)]
        
    def __getitem__(self, key):
        print(f'__getitem__ called, key : {key}'
        return self.listData[key]

data = Data()

iterData = iter(data)

for x in iterData:
    pass

# key : 0 , key : 1, ..., key : 10
# 위에 정의된 'self.listData'는 참고할 인덱스가 9까지 밖에 없지만, 10까지 조회하고
# KeyError(딕셔너리) 또는 IndexError(리스트, 세트)가 던져진 경우 이터레이션을 중단합니다.
```

### 두번째 인자 *sentinel*이 있는 경우

`iter(object, sentinel)`

첫번째 인자 *object*는 **반드시** callable 객체이어야 합니다.

이 경우에 생성된 이터레이터는 첫번째 인자 *object*로부터 차례대로 아이템을 가져오기 위해 인자 없는 \_\_next\_\_() 메서드가 호출됩니다.

간단한 파이썬 코드로 작성하면 아래와 같습니다.

```python
def iter(object, sentinel):
    
    while True:
        item =  object.next()
        
        # 2 번째 조건은 이터레이션이 모두 소진된 경우
        if (not item == sentinel) and (item is not None):
            yield item
        else:
            raise StopIteration
```


반환된 아이템이 두번째 인자 *sentinel*과 같으면 StopIteration Exception이 생성되고, 이터레이션이 멈춥니다.

그렇지 않은 경우, 이터레이션은 계속 됩니다.

## 요긴하게 쓰기

일정 구간을 읽는 코드를 쓸 때  `iter()`가 쓰기 적절합니다.

다음 예시 코드는 이진 데이터 베이스 파일을 고정된 구간을 끝까지 읽을 때 `iter()` 메서드를 사용한 코드입니다.

```python
from functools import partial

# 1) iter을 이용하는 경우

with open('mydata.db', 'rb') as f:
    # 파일의 끝(b'')에 도달하면 이터레이션을 종료합니다
    for block in iter(partial(f.read, 64), b''):
        process_block(block)
        
# 2) iter을 이용하지 않고 if 문을 이용하는 경우

with open('mydata.db', 'rb') as f:

    # python 3.8 이상인 경우
    while (block := f.read(64)) != b'':
        process_block(block)
        
    # python 3.8 미만의 경우
    while True:
        block = f.read(64)
        if block:
            process_block(block)
        else:
            break
```

이 코드는 64바이트씩 파일을 읽으면서 두번째 인자 *sentinel*인 비어있는 바이트(b''),

즉 파일의 끝까지 읽으면 for-loop을 종료하는 예시입니다.

> `partial` 메서드는 첫번째 인자로 주어지는 메서드의 여러개의 인자 중 몇개를 미리 넣어 둔 패턴입니다.

어느 패턴을 사용할지는 개인의 선택입니다.

## 파이썬 공식 문서 - 영어(원문)

`iter(object[, sentinel])`

Return an [iterator](https://docs.python.org/3/glossary.html#term-iterator) object.<br>
The first argument is interpreted very differently depending on the presence of the second argument.<br>
Without a second argument, *object* must be a collection object which supports the iteration protocol (the [\_\_iter\_\_()](https://docs.python.org/3/reference/datamodel.html#object.__iter__) method), or it must support the sequence protocol (the[\_\_getitem\_\_()](https://docs.python.org/3/reference/datamodel.html#object.__getitem__) method with integer arguments starting at `0`).<br>
If it does not support either of those protocols, TypeError is raised.<br>
If the second argument, *sentinel*, is given, then object must be a callable object.<br>
The iterator created in this case will call *object* with no arguments for each call to its [\_\_next\_\_()](https://docs.python.org/3/library/stdtypes.html#iterator.__next__) method; if the value returned is equal to *sentinel*, [StopIteration](https://docs.python.org/3/library/exceptions.html#StopIteration) will be raised, otherwise the value will be returned.

See also [Iterator Types](https://docs.python.org/3/library/stdtypes.html#typeiter).

One useful application of the second form of iter() is to build a block-reader.<br>
For example, reading fixed-width blocks from a binary database file until the end of file is reached:

```python
from functools import partial
with open('mydata.db', 'rb') as f:
    for block in iter(partial(f.read, 64), b''):
        process_block(block)
```