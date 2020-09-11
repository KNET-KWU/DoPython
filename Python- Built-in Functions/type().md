# type()

* 번역

* 사용 예시

* 원문

## 번역

```py
class type(object)

class type(name, bases, dict)
```

하나의 인자를 대입하는 경우, 객체의 타입을 반환합니다.

반환되는 객체의 타입은 **type 객체**이고 object.__class__ 메서드가 반환하는 것과 같은 객체를 반환합니다.

즉 type(object) == object.__class__ 입니다. type 그자체를 포함해서도요.

<\n>

객체의 타입을 확인할 때 isinstance() 내장함수를 사용하는 것을 권장합니다. type()과 달리 서브클래스를 포함해 검사하기 때문입니다.

객체지향 프로그램을 **제대로** 작성한다면, 리스코프 치환원칙을 통해 객체의 동일 유무를 확인할 수 있기 때문에 좋습니다!

<\n>

세개의 인자를 대입하는 경우, **새로운 타입의 객체**를 반환합니다.

type은 class를 만드는 <code>class</code> 선언문의 원초적인 형태입니다 (태초의 클래스는 이렇게 만들어졌다~).

type의 첫번째 인자, name은 문자열을 인자로 받아 클래스의 이름을 의미하고, __name__ 속성에 해당됩니다.

type의 두번째 인자, base는 튜플을 인자로 받아 클래스의 기본 구성요소를 이루며, __bases__ 속성에 해당됩니다.

type의 세번째 인자, dict는 클래스의 본체에 있는 요소들의 namespace을 구성하며, __dict__ 속성에 해당됩니다.

다음 두 선언문은 동일한 클래스 'X'를 생성합니다.

```py
class X:
    a = 1

X = type('X', (object,), dict(a=1, b=2))

print(X.a) # 1
```

> 모든 객체를 생성할 때, 명시하지 않아도 object 클래스를 상속합니다.
> 그래서 두번째 인자 base에 해당되는 튜플에 object를 선언하지 않으면 


Type Object 또한 참고하세요.

```
Type Object

<code>Type</code>  오브젝트는 오브젝트의 타입을 의미합니다.
오브젝트의 타입은 내장된 함수 type()을 통해 참고합니다.
types 객체(예: <class 'int'>)는 보여주는 것 외에 특별한 메서드나 함수가 존재하지 않습니다(보여주기만을 위해 존재).
표준 모듈 'types'는 파이썬에 내장된 모든 타입의 이름을 정의합니다.

타입은 다음과 같이 표시됩니다: type(123) # <class 'int'>
```
python 3.6 변경사항: 

<code>type</code> 클래스를 상속받는 서브클래스 중, type.__new__ 메서드를 오버라이딩하지 않는 클래스의 경우,

인자를 하나만 받고, 객체인 인자의 타입을 반환하는 메서드로 작동되지 않습니다.

## 사용 예시

```py
class A:
    def sayHi():
        print('hi!')
    
class B(A):
    pass

a = A()
b = B()

if type(a) is A:
    a.sayHi()
    
# 잘못된 예시 (1)
if type(a) is type(A):
    a.sayHi()
    
# 잘못된 예시 (2)
if type(b) is type(a):
    b.sayHi()
```

### 잘못된 사용법 (1) - 타입의 타입

<code> type(a) is type(A) </code>에 '==' 연산자가 아닌 'is' 연산자를 썼다는 것을 볼 수 있다.

단지 값으로써 동일한 type이 아닌 포인터 주소상으로도 같은 것을 보여주려고 위와 같이 작성했다.

클래스는 사용자가 정의한 **타입**이다.

그래서 type(A)를 하면 위에 언급한 'Type object'가 반환된다.

즉 타입의 타입이 반환되기 때문에 <class '__main__.A'>[^__main__] is <class 'type'> --> False가 되는 것이다.

만약 type을 이용한 제네릭(generic) 프로그래밍을 시도하는 경우 객체지향적이지 못하고, 끔찍한 코드로 발전할 가능성이 높기 때문에 안하는 것을 권장한다.

```py
type_ = type(a)
if type_ is int:
    # do something...
elif type_ is float:
    # do something...
elif type_ is str:
    # do something...
```

쓴다면 type()은 메서드의 인자가 하나의 타입 외에 허용하지 않는 용도(type checking)로 쓰길 바란다

```py
def add(a : int, b : int):
    if type(a) is int or type(b) is int:
        riase TypeError("only int arguments are allowed")
    return a + b
```

### 잘못된 사용법 (2) - 의도치 못한 객체지향적 코드 

A라는 클래스를 상속받은 클래스인지 확인하기 위해서 타입을 확인하는 시도에 type을 사용할수 있다.

하지만, 타입은 상속받은 클래스들의 종류와 상관없이 인자로 들어온 객체 자체의 클래스를 참고하므로

type()은 **리스코프 치환원칙(is-a)을 잘 지키는 객체지향적 코드**라면 쓰기에 부적절하다.

```py
class Human:
    def __init__(self, name):
        self.name
    def sayHi(self):
        print('hi!')
        
class Programmer(Human):
    def __init__(self, name):
        super().__init__(name) 

class Student(Human):
    def __init__(self, name):
        super().__init__(name)
    
ddppoo = Programmer('ddppoo')
ccppoo = Student('ccppoo')

def greetingsHuman(man : Human) -> None:
    if type(man) is Human:
        man.sayHi()
    else:
        # 모든 객체에 name 프로퍼티가 있다는 가정하
        raise TypeError(type(man).__name__  + ' is not a Human!')

greetingsHuman(ddppoo)  # TypeError : Programmer is not a Human!
greetingsHuman(ccppoo)  # TypeError : Student is not a Human!
```

만약 객체지향적 코드라면 Programmer와 Student 클래스 모두 Human을 상속하고 리스코프 치환원칙(Programmer is-a Human)을

충분히 잘 따르기 때문에 제대로 작동했어야 하는데 TypeError를 던진다.

이와 같은 경우 아래와 같이 issubclass() 메서드를 갖이 써야 의도한 작동방식을 구현할 수 있다.

```py
    if issubclass(type(man), Human):
        man.sayHi()
```

## 원문

With one argument, return the type of an object.<\n> The return value is a type object and generally the same object as returned by object.__class__.

The isinstance() built-in function is recommended for testing the type of an object, because it takes subclasses into account.

With three arguments, return a new type object.<\n> This is essentially a dynamic form of the class statement.<\n> The name string is the class name and becomes the __name__ attribute; the bases tuple itemizes the base classes and becomes the __bases__ attribute; and the dict dictionary is the namespace containing definitions for class body and is copied to a standard dictionary to become the __dict__ attribute.<\n> For example, the following two statements create identical

See also Type Objects.

Changed in version 3.6: Subclasses of type which don’t override type.__new__ may no longer use the one-argument form to get the type of an object.

-------

Type Objects
Type objects represent the various object types. An object’s type is accessed by the built-in function type(). There are no special operations on types. The standard module types defines names for all standard built-in types.

Types are written like this: <class 'int'>.

--------

## 각주

\[^__main__]: __main__처럼 앞에 붙은 것은 클래스가 위치해 있는 파일을 의미한다.

__main__은 현재 실행하고 있는 스크립트를 의미하며, 현재 스크립트 외에 import 된 클래스의 경우

예를 들어 scriptName.py에 있는 클래스 A를 import 한 뒤 type(a)를 출력하는 경우 <class 'scriptName.A'>와 같이 표시된다.