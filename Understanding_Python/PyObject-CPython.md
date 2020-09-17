# PyObject


링크 : [cpython/Include/object.h](https://github.com/python/cpython/blob/master/Include/object.h)

## 목차

* 서론

* PyObject 번역 (한글)

    * PyObject 구조체 정의 보기

* 정리 - 배운것

* PyObject 원문 (영어)

## 서론

이 글은 'id().md'를 작성하던 도중, 

어떤 객체든간에 **id 함수를 2n - 1번 감싸면 2n번 감싼 id 값과 동일**한 현상을 볼 수 있어

호기심에 id 함수의 구현부를 찾으려고 했으나 실패했고,

bulit-in 함수중 C/C++ 언어로 작성된 함수임을 파악하고 그 구조를 파고 들었으나,

끝에는 PyObject struct에 도달하게 되어 id()에 관한 미스터리와 PyObject에 대해 조사하기 위해서 쓰게된 글이다.

우선 파이썬의 태초이자 만물의 근원인 'PyObject 구조체'에 대해서 조사를 하고 미스터리를 해결하고자 한다.

## Pyobject 원문 번역 (한글)

[원문 링크](https://github.com/python/cpython/blob/0705ec8a149e27023b0420ae0366d16255f6c9f7/Include/object.h#L9)

**수정할 것 : 여기서 말하는 struct PyObject가 파이썬 코드상에 정의된 클래스(class)와 동일하면 문맥에 따라 단어 수정하자**

객체(object)와 타입(type obejct) 인터페이스

<br>

객체는 힙(heap) 메모리에 할당되는 구조체(structures) 입니다.

1. 생성된 객체가 제대로 가비지 컬렉트(garbage-collected)되도록 특수한 규칙을 적용받습니다.

2. 객체는 정적으로 할당되거나, 스택에 할당되지 않습니다; 객체는 특수한 매크로와 함수를 통해서만 접근가능합니다.

타입 객체(Type Object, 파이썬 모든 객체의 메타클래스)는 예외적으로 첫번째 규칙을 적용받지 않습니다.

파이썬 2.2 버전에서 타입과 클래스의 통합화(unification) 작업을 통해, 타입 객체도 일반 클래스와 동일하게 힙에 할당될 수 있는 타입 객체를 만들었지만,

예외적으로 타입 객체(\<class 'type'\>)는 정적으로 초기화된(statically initialized) 타입 객체를 사용합니다.

<br>

객체(object)에는 객체가 복사되거나 삭제될 때 각각 증가, 감소하는 '참조 개수(reference count)'가 존재합니다.

참조 개수가 0이되면 객체는 힙 메모리에서 해제됩니다.

<br>

객체는 어떤 종류의 데이터를 갖는지 보일 수 있는(represented) '타입'을 갖고 있습니다. 객체의 타입은 생성될 때 고정됩니다.<br>
타입 객체(Types)는 객체 그 자체로써 의미합니다.<br>
모든 객체는 객체 자신이 해당하는 타입 객체에 포인터를 가지고 있습니다.<br>
타입 객체 또한 자신이 어떤 객체인지 가리키는 타입 포인터가 존재하는데, 타입(type)의 객체 타입(object type)은 타입(type)이므로 자기 자신을 참조하는 포인터를 갖고있습니다.<br>
(역 : 그래서 파이썬에서 'print(type(type) is type) # True' 가 나옵니다)

<br>

정의된 객체는 처음 생성되었을 때 할당된 메모리 주소와 크기는 변하지 않습니다.<br>
가변적인 데이터를 갖고 있는 객체의 경우, 그 데이터의 포인터를 갖고 있기 때문에 메모리 크기의 영향은 받지 않습니다.<br>
(역: 객체에 가변적인 리스트를 속성을 갖고 있다면 이중포인터를 통해 참조한다는 뜻; PyObject -> ListPointer -> '가변길이의 데이터')<br>
모든 객체 인스턴스들이 같은 크기의 데이터를 가지고 있을 수 없지만, 처음 할당된 객체의 메모리는 재할당 될 수 없습니다.<br>
이런 제한은 참조 개수를 파악할 때 사용되는 포인터가 하나로 충분한 이점을 가지고 있습니다.

만약 객체의 크기가 변경될 때마다 기존의 포인터(P1) 불연속적으로 데이터를 저장한다면, 추가로 할당된 메모리 포인터(P2, P3, ...)를 갖고 있어야 하고, <br>
이미 객체의 포인터를 참조하고 있는 다른 객체들이 모두 업데이트 되어야하므로 (P1 -> P1, P2, ... Pn 포인터를 갖고 있어야..) 비효율적입니다.

또한 객체의 포인터에 할당된 데이터의 크기가 변경되는 경우 재할당을 하면 주변에 있는 모든 메모리 포인터를 재할당해야 하기 때문에 비효율적입니다.

그래서 모든 객체 인스턴스는 고정된 메모리 주소와 포인터를 갖고 있습니다.

<br>

객체는 **항상** 'PyObject *'를 통해 접근합니다 (CPython 구현에서).

'PyObject' 타입(구조체)에는 참조 개수와 객체의 타입을 가르키는 포인터만 갖고 있습니다.
객체 인스턴스에 해당하는 실제 메모리는 이중참조를 통해서 더 큰 구조체 타입을 가르키는 포인터를 갖고 난 뒤에 참조 가능합니다 (정의 부분-'크기 불변'과 구현 부분-'크기 가변'이 분리 되었다는 뜻).

객체의 구현 부분(가변적인 데이터 갖고 있는) 구조체에는 정의 부분이 갖고 있는(이 두개 밖에 없는) **참조 개수**와 **객체 타입 포인터**를 무조건 갖고 있습니다.<br>
그래서 PyObject_HEAD는 참조 개수와 객체 타입포인터를 갖고 있도록 매크로로 작성되었습니다 (PyObject 필수요소).<br>
이 매크로는 미래에 필요에 따라 수정될 수 있습니다.

특정 객체 타입의 구현은 객체 포인터를 옳바른 타입에 캐스트할 수 있습니다(...?)<br>
(The implementation of a particular object type can cast the object pointer to the proper type and back.)

<br>

객체 생성시 사용되는 표준 인터페이스는 객체가 생성될 때 크기가 결정되는 객체에 사용됩니다.

### PyObject 구조체 정의 보기

[원문 링크](https://github.com/python/cpython/blob/0705ec8a149e27023b0420ae0366d16255f6c9f7/Include/object.h#L105)

```C
typedef struct _object {
    _PyObject_HEAD_EXTRA
    Py_ssize_t ob_refcnt;
    PyTypeObject *ob_type;
} PyObject;
```

* _PyObject_HEAD_EXTRA
    
    * 매크로 구현 : [링크](https://github.com/python/cpython/blob/b26a0db8ea2de3a8a8e4b40e69fc8642c7d7cb68/Include/object.h#L70)

    * PyObject는 힙(heap)에 저장되고, 고정된 길이로 메모리를 할당받는다. 객체들은 배열과 같이 저장된다
    
    * 이 매크로는 Doulbe-linked-list 처럼 앞뒤 _object (PyObject) 포인터를 갖고 있다

* Py_ssize_t ob_refcnt;

    * 객체 참조 개수

* PyTypeObject *ob_type;

    * 클래스의 메타 클래스 (주로 메타클래스가되는 \<class 'type'\>이 맞다)

## 정리 - 배운것

1. 파이썬은 동적 바인딩을 지원하고 강한 타입의 언어임을 객체 구조체를 통해서 알 수 있다.

주석 - [object.h](https://github.com/python/cpython/blob/b26a0db8ea2de3a8a8e4b40e69fc8642c7d7cb68/Include/object.h#L12)에서 볼 수 있듯이 
객체는 힙에 배열처럼 나란히 고정된 크기로 저장된다.

객체 구조체에는 힙에 배열되면 앞뒤로 있는 객체의 포인터를 갖고 있어서 Double-Linked-List처럼 구현되어있다.

고정된 크기와 주소를 갖고 있으므로 메모리 참조를 할 때 포인터를 빠르게 참조할 수 있다<br>
(고정된 길이만큼 이동해서 얻을 수 있으므로, 0xAEEEEE + sizeof(PyObject)* (이동할 수) 을 통해서 빠르게 접근 가능)

CPU에서 메모리를 참조 할 때 주변 메모리를 캐싱하기 때문에 같은 크기의 객체가 줄줄이 있으므로, 캐시 히트(Cache hit) 확률이 높아진다.

그래서 모든 동작이 전체적으로 빨라진다. (GC, 참조, 등등 ...)

힙에 저장된 PyObject는 객체(인스턴스)를 가르키는 것으로 이중포인터와 같다.

2. 타입의 타입은 타입이다.

```C
// PyAPI_DATA(PyTypeObject) PyType_Type -> builtin-type 매크로를 모두 풀어쓴 결과
PyType_Type = extern __attribute__ ((visibility("default")) PyTypeObject
```

이래서 파이썬 타입의 타입이 타입인데 C언어가 익숙하지 않은것을 떠나

더 파고들기 머리아파서 여기까지 하려고한다.

C 헤더에서 타입의 타입을 타입이라고 정의했으니, 

```python
type(type) is type # True
```

는 더 이상 **마법같은 현상이 아니다**.

## 원문

Object and type object interface

Objects are structures allocated on the heap.<br>  Special rules apply to
the use of objects to ensure they are properly garbage-collected.<br>
Objects are never allocated statically or on the stack; they must be
accessed through special macros and functions only.<br>  (Type objects are
exceptions to the first rule; the standard types are represented by
statically initialized type objects, although work on type/class unification
for Python 2.2 made it possible to have heap-allocated type objects too).

An object has a 'reference count' that is increased or decreased when a
pointer to the object is copied or deleted;<br> when the reference count
reaches zero there are no references to the object left and it can be
removed from the heap.

An object has a 'type' that determines what it represents and what kind
of data it contains.<br>  An object's type is fixed when it is created.<br>
Types themselves are represented as objects; an object contains a
pointer to the corresponding type object.<br>  The type itself has a type
pointer pointing to the object representing the type 'type', which
contains a pointer to itself!.

Objects do not float around in memory; once allocated an object keeps
the same size and address.<br>  Objects that must hold variable-size data
can contain pointers to variable-size parts of the object.<br>  Not all
objects of the same type have the same size; but the size cannot change
after allocation.<br>  (These restrictions are made so a reference to an
object can be simply a pointer -- moving an object would require
updating all the pointers, and changing an object's size would require
moving it if there was another object right next to it.)<br>

Objects are always accessed through pointers of the type 'PyObject *'.<br>
The type 'PyObject' is a structure that only contains the reference count
and the type pointer.<br>  The actual memory allocated for an object
contains other data that can only be accessed after casting the pointer
to a pointer to a longer structure type.<br>  This longer type must start
with the reference count and type fields; the macro PyObject_HEAD should be
used for this (to accommodate for future changes).<br>  The implementation
of a particular object type can cast the object pointer to the proper
type and back.

A standard interface exists for objects that contain an array of items
whose size is determined when the object is allocated.