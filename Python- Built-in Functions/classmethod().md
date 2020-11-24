# classmethod()

## 목차

* 파이썬 공식 문서 - 한글(번역)
    
    * 클래스(타입 객체)와 인스턴스에서 클래스 메서드가 호출될 떄
    
    * 하는건 정적 메서드(staticmethod)와 다를게 없어보이는데 언제 쓰는거지?
    
        * 메서드가 소속되어 있는 클래스의 다른 속성을 이용할 수 있다
    
    * 다른 언어와 사뭇 다른 파이썬의 클래스 메서드
    
* 파이썬 공식 문서 - 영어(원문)

## 파이썬 공식 문서 - 한글(번역)

클래스에 정의된 일반 메서드를 클래스 메서드로 변환합니다.

A class method receives the class as implicit first argument, just like an instance method receives the instance.<br>

클래스 메서드는 암묵적으로 클래스(타입)을 첫번째 인자로 받습니다.

인스턴스를 생성하고 사용하는 일반 메서드 첫번째 인자로 `self`가 정의되어 있는 것처럼

일반 메서드 첫번째 인자 `self`에 인스턴스 자기 자신이 들어가듯이,

클래스 메서드 첫번째 인자(관용적으로 class의 줄임말인 `cls`)에 클래스(타입) 자신이 들어갑니다.

그래서 클래스 메서드를 정의하는 경우 혼동을 방지하기 위해서 아래와 같은 패턴으로 정의합니다.

```python
class C:
    @classmethod
    def f(cls, arg1, arg2, ...): ...
```

메서드 이름(classmethod) 앞에 골뱅이(@)를 합친 형태 `@classmethod`는 데코레이터(decorator) 패턴입니다.

(1) 클래스 메서드는 클래스(타입 객체)를 통해 사용하거나 (`C.f()`)

(2) 인스턴스를 통해 사용할 수 있습니다 (`C().f()`)

<br>

## 클래스(타입 객체)와 인스턴스에서 클래스 메서드가 호출될 떄

클래스(타입 객체) - (1), 인스턴스 객체 - (2)를 통해서 클래스 메서드를 호출하는 방법과 상관없이 

첫번째 인자로 **무조건** 클래스(타입 객체)가 들어가집니다 - 인스턴스의 경우 인스턴스의 클래스가 첫번쨰 인자로 대입됩니다.

```python
class Parent:
    @classmethod
    def c_method(cls, *args):
        print(f'cls : {cls}')
        print(f'args : {args}')
        
class Child(Parent):
    pass

```

위에 정의된 클래스(타입 객체)로 설명을 이어 나가겠습니다.

우선 클래스와 인스턴스가 `print()` 메서드를 통해 출력될 때(`__repr__()`) 어떻게 나오는지 비교후 보시면 되겠습니다.

```python
# 클래스(타입 객쳬)를 출력했을 경우 다음과 같이 나옵니다.
print(f'Parent type object: {Parent}')  # <class '__main__.Parent'>

# 인스턴스를 출력했을 경우 다음과 같이 나옵니다.
print(f'Parent instance object : {Parent()}')   # <__main__.Parent object at 0x11afdfe10>

print('call c_method by class')

Parent.c_method()   # cls : <class '__main__.Parent'>

print('call c_method by instance')  # cls : <class '__main__.Parent'>

Parent().c_method()
```

상속한 클래스와 클래스의 인스턴스의 경우도 동일합니다.

```python
print(f'Child type object: {Child}')    # <class '__main__.Child'>
print(f'Child instance object : {Child()}') # <__main__.Child object at 0x1120c8550>

print('call c_method by class')

Child.c_method()    # cls : <class '__main__.Child'>

print('call c_method by instance')

Child().c_method()  # cls : <class '__main__.Child'>
```

상속을 통해서 만들어진 클래스에서 클래스 메서드를 호출하는 경우, 상속한 클래스가 첫번째 인자로 주어집니다.

<br>

## 하는건 정적 메서드(staticmethod)와 다를게 없어보이는데 언제 쓰는거지?

(1) **인스턴스 없이** 메서드를 사용할 수 있다는 점

(2) 논리적으로 **클래스를 기준으로 메서드를 분류**할 수 있다는 점(예 : itertools의 메서드들)

이 두가지 특징 모두 클래스 메서드와 정적 메서드 가지고 있습니다.

정적 메서드와 클래스 메서드의 차이를 설명하겠습니다.

### 1. 메서드가 소속되어 있는 클래스의 다른 속성을 이용할 수 있다.

*클래스 메서드*의 첫번째 인자에 자동으로 클래스(타입 객체)가 대입되는 것을 위에서 보셨을 것입니다.

첫번째 인자로 클래스가 주어지기 때문에 *클래스 메서드*를 정의할 때 속해있는 클래스를 참조할 수 있습니다.

```python
class MyClass:
    
    data = 123
    
    @classmethod
    def c_method(cls, *args):
        print(f'MyClass data : {cls.data}')
        
    @staticmethod
    def s_method(*args):
        print(f'MyClass data : {MyClass.data}')
        
MyClass.c_method    # 123

MyClass.s_method    # 123
```

정적 메서드와 달리 클래스 메서드는 인스턴스가 클래스에 정의된 속성을 이용할 때 `self`을 사용하듯이

인스턴스의 클래스 혹은 클래스 자기 자신을 뜻하는 `cls`을 사용하시는 것을 볼 수 있습니다.

물론 예시에서 볼 수 있듯이 정적 메서드 또한 `cls`가 아닌 클래스 이름을 통해 직접 참조할 수 있지만(`MyClass.data`)

클래스 내부에 속해있는(정의되어 있는) 또 엄연히 말하면 클래스에 속해있는 메서드 자신(s_method)이 속해있는 클래스(MyClass)의 속성을 사용하기 위해서

다시 클래스(MyClass)를 통해 참조하는 것 자체는 정적 메서드에 옳지 않은 형태입니다.

오히려 클래스 내부에 속성을 사용하는 메서드라면 클래스 메서드(classmethod)으로 작성되어야 하는 것이 옳바른 형태입니다.

<br>

**정리** : 

정적 메서드도 클래스 메서드처럼 클래스의 속성을 참조 할 수 있지만, 관례적으로 정적 메서드는 홀로 작동하는(stand alone) 메서드로 간주하고

클래스에 속해있는 정적 메서드의 경우 단지 *그럴듯한 이름의 클래스에 속해 있을것 같으니* 같이 정리해준 것으로 보시면 됩니다.

**참고** : 

MyClass 예시에서 정적 메서드의 네임 스페이스(name space)는 

'**s_method** 정의 구간(인자 포함)' -> 'MyClass 내부' -> 'global' 순이기 때문에

MyClass이라는 똑같은 이름을 가진 클래스가 import 되어도 문제는 일어나지 않습니다.

<br>

## 다른 언어와 사뭇 다른 파이썬의 클래스 메서드

C++ 또는 Java의 staticmethod을 생각하신다면 기본 메서드 - staticmethod를 찾아보시면 됩니다.

<br>

## 파이썬 공식 문서 - 영어(원문)

Transform a method into a class method.

A class method receives the class as implicit first argument, just like an instance method receives the instance.<br>
To declare a class method, use this idiom:

```python
class C:
    @classmethod
    def f(cls, arg1, arg2, ...): ...
```

The `@classmethod` form is a function [decorator](https://docs.python.org/3/glossary.html#term-decorator) – see [Function definitions](https://docs.python.org/3/reference/compound_stmts.html#function) for details.

A class method can be called either on the class (such as `C.f()`) or on an instance (such as `C().f()`).<br>
The instance is ignored except for its class.<br>
If a class method is called for a derived class, the derived class object is passed as the implied first argument.

Class methods are different than C++ or Java static methods.<br>
If you want those, see [staticmethod()](https://docs.python.org/3/library/functions.html#staticmethod).

For more information on class methods, see [The standard type hierarchy](https://docs.python.org/3/reference/datamodel.html#types).