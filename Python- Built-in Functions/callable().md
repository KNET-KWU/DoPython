# callable()

## 목차

* 파이썬 공식 문서 - 한글(번역)
    
    * callable는 무엇일까
    
    * callable가 True를 반환하는 경우
    
        * cpython으로 callable 깊게 보기
        
        * callable가 True를 반환했지만 호출이 실패하는 경우
        
    * callable가 False를 반환하는 경우
    
* 파이썬 공식 문서 - 영어(원문)

<br>

## 파이썬 공식 문서 - 한글(번역)

`callable(object)`

첫번째 인자 `object`가 호출 가능(callable)하다면 `True`아니면 `False`를 반환합니다.

대표적으로 잘알려진(혹은 직관적으로 알 수 있는) 호출(call)가능한 객체는 타입(클래스)과 함수(function)입니다.<br>
(파이썬의 모든 것은 객체인 점을 잊지마세요)

```python
def myFunc():
    print('Hello')

callable(myFunc)    # True

class MyType:
    pass
    
callable(MyType)    # True
```

`callable(object)`가 `True`를 반환해도 호출이 **실패할 수도** 있습니다.

`False`를 반환하는 경우 호출은 무조건 될 수 없습니다.

우선 간단하게 callable이란 무엇을 의미하는지 짧게 설명하겠습니다.

<br>

## callable는 무엇일까

CPython으로 따지면 tp_call 포인터가 Null인 아닌 경우,

파이썬으로 따지면 함수처럼 쓸 수 있는 `MyMethod()` 것이라고 보시면 됩니다.
<br>

## callable가 True를 반환하는 경우

타입 객체, 즉 정의된 클래스(위 예시에서 'MyType')의 경우 인스턴스를 반환하는 `__call__` 메서드가 정의되어 있습니다.

그래서 타입 객체(클래스) 같은 경우 callable입니다.

한마디로 정리하자면 `__call__`이 정의되어 있는 객체는 `callable` 메서드가 `True`를 반환합니다.

`callable(object)`메서드를 파이썬으로 만들 수 있다면 다음과 유사합니다.

```python
def callable(obj):
    return hasattr(obj, '__call__')
```

<br>

### cpython으로 callable 깊게 보기

[cpython/Objects/object.c](https://github.com/python/cpython/blob/0e62efc51e31c741b61604787aeab4936e6e50e4/Objects/object.c#L1423)의 callable를 확인하는 함수는 아래와 같습니다. 

```c
int
PyCallable_Check(PyObject *x)
{
    if (x == NULL)
        return 0;
    return Py_TYPE(x)->tp_call != NULL;
}
```

사실 파이썬 차원에서 객체를 저장하고 있는 `PyObject` 구조체 포인터를 NULL로 만들 수 있을지 모르겠습니다.

객체가 메모리에서 해제된 경우 `NULL`이 인자로 들어올 것이므로 첫번째 if문은 이해하실수 있을거라고 믿습니다.

두번째 조건을 보면 cpython의 타입(type) 구조체의 `tp_call` 멤버의 Null 인지 아닌지를 파악하는 것입니다.

`tp_call`는 cpython을 구성하는 기본 구조체중 [PyTypeObject](https://docs.python.org/3/c-api/typeobj.html?highlight=tp_call#c.PyTypeObject.tp_call)의 멤버 중 하나로 tp는 type의 줄임말입니다.

파이썬으로 `__call__`이 정의되어 있다면 `tp_call`에 호출할 함수의 포인터가 있을것이므로 Null이 아닙니다

그래서 callable일 경우 `Py_TYPE(x)->tp_call != NULL;`의 값으로 True가 반환되는 것입니다.

<br>

### callable가 True를 반환했지만 호출이 실패하는 경우

여러분이 실제로 `callable` 메서드가 True를 반환했는데 호출(call)에 실패할 확률은 매우 낮습니다.

굳이 일부러 할 확률은 매우 낮을 뿐더러 우리가 일반적으로 사용하는 패턴과 매우 다르기 때문이죠.

예시를 보시면 한번에 납득이 되실 겁니다.

```python
# callable 메서드가 True를 반환하지만 실제로 호출이 실패하는 경우

class MyClass:
    
    @classmethod
    def c_method(*args):
        print('class method')
        
    @staticmethod
    def s_method(*args):
        print('static method')

# ----------- classmethod case ------------- #
        
callable(MyClass.c_method)    # True
     
MyClass.c_method()    # class method
              
callable(MyClass.__dict__['c_method'])  # False

MyClass.__dict__['c_method']()    # TypeError: 'classmethod' object is not callable

# ----------- staticmethod case ------------- #

callable(MyClass.s_method)    # True
     
MyClass.s_method()    # static method
              
callable(MyClass.__dict__['s_method'])  # False

MyClass.__dict__['s_method']()    # TypeError: 'staticmethod' object is not callable
```

[파이썬 버그 이슈 #20309](https://bugs.python.org/issue20309)에서 논의 되어있는 것을 정리하자면

1. 위와 같이 메서드를 사용하는 패턴이 잘 사용하지 않는 패턴이라 나중에 추가로 유지보수할 필요성이 낮다(중요한 거 아닌건 일 늘리지 말자).

2. 클래스의 \_\_dict\_\_ 참조하는 cpython 구현부는 당시 귀도(파이썬 창시)가 한번에 작성했는데, 
이렇게 파이썬을 작성했는데, 이렇게 작동하도록 썼을 이유가 나름대로 있을거다.<br>
- 파이썬의 주요 기여자(Raymond Hettinger) 커멘트 중 -

라는 이유로 위와 같은 현상에 대해서 더이상 논하지 않게 되었습니다(현행유지).

크게 중요한 내용은 아니고 흔히 접하는 경우가 아니므로 이런 예외 정도가 있다~ 라는 정도만 알고 계시면 되겠습니다.

## callable가 False를 반환하는 경우

`False`를 반환하는 경우 객체에 `__call__` 메서드가 정의되어 있지 않은 객체를 의미합니다.

일반적으로 파이썬을 이용하면서 `myObject()`처럼 옆에 괄호`()`를 하지 않는 것들을 의미합니다.

```python
num = 23
num()   # TypeError: 'int' object is not callable

name = 'ccppoo'
name()  # TypeError: 'str' object is not callable
```

-----

파이썬  3.2 버전 변경: 

이 메서드는 파이썬 버전 3.0에 삭제되었다가 버전 3.2에 다시 만들어졌습니다.

## 파이썬 공식 문서 - 영어(원문)

callable(object)

Return [True](https://docs.python.org/3/library/constants.html#True) if the *object* argument appears callable, [False](https://docs.python.org/3/library/constants.html#False) if not.<br>
If this returns `True`, it is still possible that a call fails, but if it is `False`, calling *object* will never succeed.<br>
Note that classes are callable (calling a class returns a new instance); instances are callable if their class has a [\_\_call\_\_()](https://docs.python.org/3/reference/datamodel.html#object.__call__) method.

New in version 3.2: This function was first removed in Python 3.0 and then brought back in Python 3.2.