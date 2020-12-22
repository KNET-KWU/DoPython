# dict()

dictionary의 줄임말, 딕셔너리 객체 생성자

## 목차

* 파이썬 공식 문서 - 한글(번역)

    * 원래 아는 방식대로 만드는 경우

    * Key Word Arguments(kwargs) 처럼 딕셔너리를 만드는 경우
    
    * 튜플 리스트로 만드는 경우

    * dict-comprehension 으로 만드는 경우    


* 파이썬 공식 문서 - 영어(원문)

## 파이썬 공식 문서 - 한글(번역)

딕셔너리 객체를 만드는 방법은 가지가 있습니다

### 원래 아는 방식대로 만드는 경우

{ <키> : <값>  } 형태로 만드는 것입니다.

### Key Word Arguments(kwargs) 처럼 딕셔너리를 만드는 경우

함수의 인자를 대입하는 것처럼 딕셔너리를 만들 수 있습니다.

```python
def func(a : int = 1, b : int = 2, c : int = 3) -> int:
    return a + b + c

# kwargs를 사용해서 함수에 인자를 대입하는 모습
func(a=10, b=20, c=30)  # 60


a = dict(name = 'ccppoo', age = 333)
print(a)    # {'name': 'ccppoo', 'age': 333}
```

### 튜플 리스트로 만드는 경우

튜플로 구성된 리스트로 `dict`를 생성할 수 있습니다.

튜플의 첫번째 값은 `key`, 두번째 값은 `value`입니다.

```python
a= dict([('name', 'ccppoo'), ('age', 333)])
print(a)    # {'name': 'ccppoo', 'age': 333}
```

### dict-comprehension 으로 만드는 경우

리스트 컴프리헨션과 같은 맥락으로 딕셔너리 또한 만들 수 있습니다.

```python
key = ['aa', 'bb', 'cc']

a = {key : n for key, n in enumerate(key)}
print(a)# {0: 'aa', 1: 'bb', 2: 'cc'}
```

<br>

------

## 파이썬 공식 문서 - 영어(원문)

`class dict(**kwarg)`<br>
`class dict(mapping, **kwarg)`<br>
`class dict(iterable, **kwarg)`<br>

Create a new dictionary.<br>
The [dict](https://docs.python.org/3/library/stdtypes.html#dict) 
object is the dictionary class. See [dict](https://docs.python.org/3/library/stdtypes.html#dict) 
and [Mapping Types -- dict](https://docs.python.org/3/library/stdtypes.html#typesmapping) for documentation about this class.

For other containers see the built-in 
[list](https://docs.python.org/3/library/stdtypes.html#list), [set](https://docs.python.org/3/library/stdtypes.html#set), 
and [tuple](https://docs.python.org/3/library/stdtypes.html#tuple) classes, as well as the [collections](https://docs.python.org/3/library/collections.html#module-collections) module.