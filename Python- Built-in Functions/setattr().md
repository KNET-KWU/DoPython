# setattr()

set attribute

속성을 초기화(설정)한다.

## 목차

* 파이썬 공식 문서 - 한글(번역)

    * Private Attribute도 추가해줘
    
* 파이썬 공식 문서 - 영어(원문)

## 파이썬 공식 문서 - 한글(번역)

`setattr(object, name, value)`

`setattr(object, name, value)` 메서드는  [getattr()](https://docs.python.org/3/library/functions.html#getattr) 메서드와 정반대의 기능을 하는 메서드입니다.<br>

첫번째 인자 `object`에 세번째 인자 `value`값을 가진 두번째 인자 `name`이라는 이름을 가진 속성을 만듭니다.

```python
class MyClass:
    number = 100
    
setattr(MyClass, 'data', 42)

MyClass.data    # 42

MyClass.__dict__    # mappingproxy({ ..., 'number': 100, 'data': 42}), data가 추가된 것을 볼 수 있습니다
``` 

<br>

클래스에 이미 동일한 이름의 속성이 있는 경우 덮어씁니다(overwrite)

```python
class MyClass:
    number = 1007

setattr(MyClass, 'number', 333)

MyClass.number  # 333
```

<br>

`setattr` 메서드는 등호('=')를 이용해서 직접 값을 대입하는 것과 같습니다.

```python
# 같은 동작을 수행합니다

setattr(MyClass, 'age', 999)

MyClass.age = 999
``` 

## 파이썬 공식 문서 - 영어(원문)

`setattr(object, name, value)`

This is the counterpart of [getattr()](https://docs.python.org/3/library/functions.html#getattr).<br>
The arguments are an object, a string and an arbitrary value.<br>
The string may name an existing attribute or a new attribute.<br>
The function assigns the value to the attribute, provided the object allows it.<br>
For example, `setattr(x, 'foobar', 123)` is equivalent to `x.foobar =123`.