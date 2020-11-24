# isinstance()

`isinstance(obj, cls)`

첫번째 인자 `obj`가 두번째 인자(클래스, 타입 객체) `cls`의 인스턴스인가요?

## 목차

* 파이썬 공식 문서 - 한글(번역)

    * 객체지향스럽게 파이썬 쓰기
    
* 파이썬 공식 문서 - 영어(원문)

## 파이썬 공식 문서 - 한글(번역)

`isinstance(object, classinfo)`

첫번째 인자 *object*가 두번째 인자 *classinfo*의 인스턴스일 경우 `True`를 반환합니다.

```python
class C:
    pass
    
isinstance(C(), C)  # True
```

<br>

*classinfo*가 *object* 클래스의 슈퍼클래스의 경우, 또는 베이스 클래스의 경우에도 `True`를 반환합니다.

```python
class E(base = object):
    pass

class C(E):
    pass
    
c = C()

isinstance(c, C)    # True
isinstance(c, E)    # True
isinstance(c, object)   # True
```

<br>

*object*가 *classinfo*의 인스턴스가 아닌 경우 `False`를 반환합니다.

*classinfo*에 여러개의 클래스 타입이 담긴 튜플이 주어진 경우, *object*가 여러 클래스 중 인스턴스인 경우 `True`를 반환합니다.

```python
class N:
    pass

class F:
    pass

isinstance(c, (C, E))    # True

isinstance(c, (C, E, N))    # True

isinstance(c, (F, N))    # False
```

<br>

*classinfo*에 클래스 타입 또는 클래스 타입이 담긴 튜플이 안주어지는 경우 [TypeError](https://docs.python.org/3/library/exceptions.html#TypeError)를 던집니다.

```python
isinstance(c, 10)
TypeError: isinstance() arg 2 must be a type or tuple of types
```

### 객체지향스럽게 파이썬 쓰기

파이썬에서 `isinstace()`는 자바 또는 다른 언어들에서 타입 캐스팅을 시도하는 행위와 비슷하다고 보시면 됩니다.

```java
// 이런 경우가 있으면 안좋은 상황이긴 하지만...
try:
    (Family) someone
except:
    continue
```

파이썬 예시를 봅시다.

```python
class Family:
    pass
    
class Parent(Family):
    # 생략
    pass
    
Mom = Parent()
Dad = Parent()

class Other:
    pass
    
# bywalker - 행인
class byWalker(Other):
    # 생략
    pass
```

위와 같이 비어 있는 클래스를 만들어서 상속을 시키면 타입에 따라 같은 이름의 메서드를 가지고 있어도 다르게 대응할 수 있습니다.

```python
people = [Mom, Dad, byWalker1 ]

for man in people:
    if isinstance(man, Family):
        man.Ask('How are you doing')
    else:
        pass
```

덕타이핑은 객체의 타입을 무시하는 **객체지향스럽지 못한** 파이썬의 특징입니다(개인적인 견해).

## 파이썬 공식 문서 - 영어(원문)

isinstance(object, classinfo)

Return `True` if the *object* argument is an instance of the *classinfo* argument, or of a (direct, indirect or [virtual](https://docs.python.org/3/glossary.html#term-abstract-base-class)) subclass thereof.<br>
If *object* is not an object of the given type, the function always returns `False`.<br>
If *classinfo* is a tuple of type objects (or recursively, other such tuples), return `True` if *object* is an instance of any of the types.<br>
If *classinfo* is not a type or tuple of types and such tuples, a [TypeError](https://docs.python.org/3/library/exceptions.html#TypeError) exception is raised.
