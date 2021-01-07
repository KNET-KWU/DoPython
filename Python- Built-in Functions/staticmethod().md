# staticmethod()

## 목차

* 파이썬 공식 문서 - 한글(번역)
    
* 파이썬 공식 문서 - 영어(원문)

<br>

## 파이썬 공식 문서 - 한글(번역)

`@staticmethod`

### 1. 클래스에 정의된 메서드를 정적 메서드(static method)로 변환합니다.

`@staticmethod`는 데코레이터(decorator) 메서드로 일반 메서드와 달리 데코레이터 문법을 이용해서 사용합니다.

```python
class C:
    @staticmethod
    def f(arg1, arg2, ...): ...
```

메서드 정의 부분(`def ... `) 위에 `@ + <데코레이터 메서드>` 작성하여 데코레이터 메서드 패턴을 이용하면됩니다.

### 2. 정적 메서드는 암묵적으로 파이썬 클래스 메서드에 쓰이는 첫번째 인자 `self`(인스턴스를 가르키는)를 사용하지 않습니다.

```python
class Sample:
    
    def __init__(self, num: int):
        self.num = num
        
    def getNum(self):
        return self.num

    @staticmethod
    def get10():
        return 10
        
print(Sample(20).getNum())    # 20

print(Sample.get10) # 10
```

### 3. 그 외 파이썬 정적메서드에 관해서

정적 메서드는 클래스 또는 인스턴스를 통해서 호출(called)될 수 있습니다.

```python
# class Sample 예시에 이어서

print(Sample(20).get10)    # 10

print(Sample.get10)         # 10
```

인스턴스의 MRO(Method Resolution Order)에는 클래스(`Sample 클래스`)가 포함되어 있기 때문입니다.<br>
(* MRO는 인스턴스 또는 클래스에 점(`.`)을 통해 메서드 또는 속성(property)를 찾을때 찾는 순서)

. . . . . . .

파이썬의 정적 메서드는 자바(Java) 또는 C++에서 사용되는 방식과 유사합니다.

정적 메서드는 클래스 이름이라는 스코프(Scope)에 묶인 일반 메서드(global scope에서 정의된 메서드)와 같다고 볼 수 있습니다.

유사한 기능을 가진 메서드를 단지 클래스라는 묶음(package)에 담았다고 봐도 무방합니다.

그래서 데코레이터로 만들어진 클래스메서드(classmethod)와 일반 클래스 메서드와 달리 정적인 메서드는

어느 상태(state - 클래스, 인스턴스)에 종속되어 있지 않은, 독립된 기능을 가진 하나의 메서드라고 볼 수 있습니다.

물론 클래스와 완전히 동떨어진 경우는 잘못 만들어진 정적 메서드입니다.

```python
class Sample

    # 일반 메서드
    def doMethod(self, *args, **kwargs):
        pass
        
    # 클래스 메서드
    @classmethod
    def doClassMethod(cls, *args, **kwargs):
        pass
       
    # 정적 메서드
    @staticmethod 
    def doStaticMethod(*args, **kwargs):
        pass
```

### 4. 데코레이터가 아닌 메서드처럼 사용하는 경우

일반 데코레이터와 같이 `staticmethod` 또한 일반 함수처럼 사용될 수 있고, 그 반환된 객체 또한 사용할 수 있습니다.

이러한 패턴은 메서드가 인스턴스의 메서드로 전환되는 것을 막되, 클래스 본체로부터 함수의 참조가 필요한 경우 사용됩니다.

위와 같은 경우 아래의 패턴을 사용하면 됩니다.

```python
class C:
    builtin_open = staticmethod(open)
```

자세한 내용은 상위 폴더 `Understanding Python`의 `Decorator and @staticmethod, @classmethod.md`를 참조해주세요

<br>

-------

## 파이썬 공식 문서 - 영어(원문)

`@staticmethod`

Transform a method into a static method.

A static method does not receive an implicit first argument.<br>
To declare a static method, use this idiom:

```python
class C:
    @staticmethod
    def f(arg1, arg2, ...): ...
```

The `@staticmethod` form is a function [decorator](https://docs.python.org/3/glossary.html#term-decorator) – see [Function definitions](https://docs.python.org/3/reference/compound_stmts.html#function) for details.

A static method can be called either on the class (such as `C.f()`) or on an instance (such as `C().f()`).

Static methods in Python are similar to those found in Java or C++.<br>
Also see [classmethod()](https://docs.python.org/3/library/functions.html#classmethod) for a variant that is useful for creating alternate class constructors.

Like all decorators, it is also possible to call `staticmethod` as a regular function and do something with its result.<br>
This is needed in some cases where you need a reference to a function from a class body and you want to avoid the automatic transformation to instance method.<br>
For these cases, use this idiom:

```python
class C:
    builtin_open = staticmethod(open)
```

For more information on static methods, see [The standard type hierarchy](https://docs.python.org/3/reference/datamodel.html#types).