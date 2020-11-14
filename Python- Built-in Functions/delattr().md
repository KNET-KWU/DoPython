# delattr()

delattr

delete + attribute

속성을 제거하다

## 목차

* 파이썬 공식 문서 - 한글(번역)
    
* 파이썬 공식 문서 - 영어(원문)

## 파이썬 공식 문서 - 한글(번역)

`delattr(object, name)`

`delattr(object, name)` 메서드는  [setattr()](https://docs.python.org/3/library/functions.html#setattr) 메서드와 정반대의 기능을 하는 메서드입니다.<br>

첫번째 인자 `object`에서 두번째 인자 `name`에 해당하는 속성을 제거합니다.

`delattr` 메서드는 사용자가 정의한 클래스(타입)와 그 인스턴스 객체에 사용할 수 있습니다.

```python
class MyClass:
    classAttribute = 'ccppoo'
    
    def __init__(self):
        self.instanceAttribute = 'Hello'
        
ss = MyClass()

# 1.delattr 메서드를 사용하기 전에 가지고 있는 속성값을 확인합니다.

dir(MyClass)    # [ ..., __weakref__ , 'classAttribute']
dir(ss)         # [ ..., __weakref__ , 'classAttribute', 'instanceAttribute']

# 2.delattr 메서드를 이용해 속성을 제거합니다

delattr(MyClass, 'classAttribute')
delattr(ss, 'instanceAttribute')

# 3.속성값이 없어진 것을 확인합니다.

'classAttribute' in dir(MyClass)    # False
'instanceAttribute' in dir(ss)      # False
```

하지만, 클래스의 **private 속성**과 존재하지 않는 속성의 이름을 두번째 인자로 대입한 경우 AttributeError를 던집니다.

```python
class MyClass:
    ## 언더바 두개로 시작하는 속성은 private 속성으로 인식됩니다.
    __privateAttr = 'My Secret'
    name = 'ccppoo'
    
delattr(MyClass, '__privateAttr')   # AttributeError: __privateAttr

delattr(MyClass, 'age')             # AttributeError: age
```

`delattr` 메서드는 `del` 키워드를 사용한 것과 같습니다

```python
# 같은 동작을 수행합니다

delattr(MyClass, 'name')

del MyClass.name
```

## 파이썬 공식 문서 - 영어(원문)

delattr(object, name)

This is a relative of [setattr()](https://docs.python.org/3/library/functions.html#setattr).<br>
The arguments are an object and a string.<br>
The string must be the name of one of the object’s attributes.<br>
The function deletes the named attribute, provided the object allows it.<br>
For example, `delattr(x, 'foobar')` is equivalent to `del x.foobar`.