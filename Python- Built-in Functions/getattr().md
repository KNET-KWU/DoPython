# getattr()

## 목차

* 파이썬 공식 문서 - 한글(번역)
    
    * getattr()과 유사한 자바스크립트의 옵셔널 체이닝('?.') 

    * getattr()과 \_\_getattr\_\_, \_\_getattribute\_\_
            
* 파이썬 공식 문서 - 영어(원문)

## 파이썬 공식 문서 - 한글(번역)

getattr(object, name[, default])

첫번째 인자 *object*에서 두번째 인자 *name*에 해당하는 속성(attribute)를 반환합니다.

문자열이 객체의 속성 중 이름이 같은 경우, 이름이 문자열과 같은 속성의 값을 반환합니다.

예를 들어 `getattr(x, 'foobar')`는 `x.foobar`와 같습니다.

```python
class MyClass:
    foobar = 'Class property'
    def __init__(self):
        self.foobar = 'ccppoo'
        
x = MyClass()

# 인스턴스에 사용하는 경우 - 아래 두 줄은 똑같이 작동합니다
x.foobar    # ccppoo
getattr(x, 'foobar')    # ccppoo

# 클래스(타입)에 사용하는 경우 - 아래 두 줄은 똑같이 작동합니다
MyClass.foobar  # Class property
getattr(MyClass, 'foobar')  # Class property
```

...

두번째 인자와 같은 이름의 속성이 존재하지 않는 경우 세번째 인자 *defualt*가 반환됩니다.<br>
세번째 인자가 주어지지 않았으면 [AttributeError](https://docs.python.org/3/library/exceptions.html#AttributeError)를 던집니다.

```python
# 위 예시 코드에 이어서 

x.myName    #   AttributeError: 'MyClass' object has no attribute 'myName'

# 'myName' 속성이 없으므로 Default 값인 None이 반환됩니다
getattr(x, 'myName', None)  # None


MyClass.myName  # AttributeError: type object 'MyClass' has no attribute 'myName'

getattr(MyClass, 'myName', None)    # None
```

### getattr()과 유사한 자바스크립트의 옵셔널 체이닝('?.') 

우선 옵셔널 체이닝은 파이썬에서 구현된 기능이 아닙니다.

```javascript
let user = {name : 'ccppoo'}

console.log(user?.id)   # undefined
```

객체에 참조하려는 해당하는 이름의 속성이 없으면 `undefined`를 반환합니다.

파이썬의 `getattr()`로 자바스크립트의 옵셔널 체이닝을 다음과 같이 표현할 수 있습니다.

```python
user = { name : 'ccppoo' }

print( getattr(user, 'id', None))
```

왜 썼냐고요?

파이썬도 자바스크립트의 옵셔널 체이닝을 도입하려는 시도([PEP 제안서](https://www.python.org/dev/peps/pep-0505/)) 
있었지만 도입은 안돼서 아쉬운 마음에 써봤습니다.

### getattr()과 \_\_getattr\_\_, \_\_getattribute\_\_

일반적인 함수가 아닌 클래스를 정의할 때 재정의할 수 있는 매직 메서드 \_\_getattr\_\_와 \_\_getattribute\_\_를 한번쯤 접하셨을 수 있습니다.

이번장에서 설명한 `getattr()`과 이름은 같지만, 존재자체에 있어 성격이 다른 메서드입니다.

특히 \_\_getattribute\_\_의 경우 조금 더 깊이 들어가는 내용이기 때문에 '대충 알면 다친다'라는 표현이 가장 어올릴 것 같습니다.<br>
그러므로 제대로 알아보기 위해서 따로 글을 작성하겠습니다(TODO : 했습니다).

[\_\_getattr\_\_]()

[\_\_getattribute\_\_]()

## 파이썬 공식 문서 - 영어(원문)

`getattr(object, name[, default])`

Return the value of the named attribute of *object*.<br>
*name* must be a string.<br>
If the string is the name of one of the object’s attributes, the result is the value of that attribute.<br>
For example, `getattr(x, 'foobar')` is equivalent to `x.foobar`.<br>
If the named attribute does not exist, default is returned if provided, otherwise [AttributeError](https://docs.python.org/3/library/exceptions.html#AttributeError) is raised.
