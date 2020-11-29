# issubclass()

`isinstance(cls1, cls2)`

첫번째 인자(클래스, 타입 객체) `cls1`이 두번째 인자(클래스, 타입 객체) `cls2`의 서브클래스인가요?

## 목차

* 파이썬 공식 문서 - 한글(번역)
    
* 파이썬 공식 문서 - 영어(원문)

## 파이썬 공식 문서 - 한글(번역)

`issubclass(class, classinfo)`

첫번째 인자 *class*가 *classinfo*의 서브클래스인 경우 `True`를 반환합니다.

가끔 인자 위치가 햇갈리는데 인자의 순서 그대로 'A가 B의 서브클래스인가?' -> `issubclass(A, B)`라고 외우시면 편합니다.

```python
class B: pass
class A(B): pass

issubclass(A, B)
```

<br>

클래스 그 자체 스스로 서브클래스로 인식합니다. 마치 수학에서 집합 A의 부분집합에도 A가 포함되듯이 말이죠.

```python
class A:    pass

issubclass(A, A)    # True
```

<br>

직접적으로 상속받지 않은 클래스의 경우에도 상속이 여러 클래스를 거쳐 상속된다면 `True`를 반환합니다.

```python
class A:
    pass
    
class B(A):
    pass
    
class C(B):
    pass
    
issubclass(C, A)    # True
```

<br>

두번째 인자 `classinfo`는 타입(클래스)가 담긴 튜플들이 주어진 경우, 튜플 내에 있는 모든 클래스가 서브 클래스인지  확인합니다.

튜플의 아이템을 차례대로 검사하기 때문에 튜플의 첫번째 원소가 서브클래스인 경우, 다른 인자가 클래스가 아니여도 에러가 발생하지 않습니다.

```python
# 위 예시코드에 이어서

issubclass(C, (A, B, int, float, str))  # True

issubclass(C, (A, "I'm", 'not', 'class'))   # True

issubclass(C, ("I'm", 'not', 'class', A))   # TypeError: issubclass() arg 2 must be a class or tuple of classes
```

## 파이썬 공식 문서 - 영어(원문)

issubclass(class, classinfo)

Return `True` if *class* is a subclass (direct, indirect or [virtual](https://docs.python.org/3/glossary.html#term-abstract-base-class)) of *classinfo*.<br>
A class is considered a subclass of itself.<br>

*classinfo* may be a tuple of class objects, in which case every entry in *classinfo* will be checked.<br>
In any other case, a [TypeError](https://docs.python.org/3/library/exceptions.html#TypeError) exception is raised.