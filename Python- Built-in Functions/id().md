# id()

## 목차

* 파이썬 공식 문서 - 한글(번역)

    * 사용 예시

    * 예외

        * 정수(int)
        
        * 문자열(str)
        
        * 불변 객체

    * 오해할 수 있는 것들

        * 잘못된 id 값 비교 연산
    
* 파이썬 공식 문서 - 영어(원문)

## 파이썬 공식 문서 - 한글(번역)

```python
id(object)
```

객체의 정체성('identity')를 반환합니다.

객체의 생명주기(메모리에서 해제되기 전)동안 주어진 유일한 상수 정수값입니다.

생명주기가 겹치지 않는 객체간에 id값은 중복될 수도 있습니다(의미는 없겠지만).

CPython에서 의미: 객체의 id값은 메모리 주소를 의미합니다.

### 사용 예시

id 함수는 고유의 정수값을 의미하기 때문에, 파이썬 개발자간에 메모리 주소와 일맥상통하는 의미로 쓰입니다.

```python
# function pass by reference? or pass by value?

class C:
    pass

dummy = C()
dummyID = id(dummy)

def foo(c):
    print(id(c) == dummyID)
    
foo(dummy) # True -> 참조를 통한 전달
```

id 값은 참조 복사, 값 복사를 확인하기 위해서(익숙하지 않은 경우) id 값을 비교하거나

```
# Singleton

class Singleton(object):
    def __new__(cls):
        if not hasattr(cls, 'instance'):
            cls.instance = super(Singleton, cls).__new__(cls)
        return cls.instance

s1 = Singleton()
s2 = Singleton()

print(f's1 is s2 : {s1 is s2}') # True
```

싱글톤 프로그램을 하는 경우 확인용도로 쓰입니다.

### 예외

파이썬은 모든 것이 객체이기 때문에 id 값을 가지지만, 예외적으로 같은 id를 가지는 객체들이 있습니다.

대표적으로 정수(int)와 불변객체들이 존재합니다.

#### 정수(int)

```python
# 지정된 id 값을 가지는 정수 확인하기
for x in range(-10, 260,1):
    a, b = int(x), int(x)

    # is 연산자는 객체의 id 값을 비교합니다
    #if( id(a) != id(b) ):
    if( a is not b):
        print(f'a: {a} | b: {b}, id(a): {id(a)} | id(b): {id(b)}')
```

-5 부터 256까지 고정된 id 값을 가집니다.

#### 문자열(str)

```python
h1 = 'Hello'
h2 = 'Hello'
h1 is h2 # True

Korean1 = '아'
Korean2 = '아'
Korean1 is Korean2 # False

Greek1 = 'ß'
Greek2 = 'ß'
Greek1 is Greek2 # True
```

영어, 그리스어(특수)는 같은 id 값을 가지지만, 이상하게도 한글은 id 값이 다르게 나옵니다 - '왜 그런지는 모르겠다 코어를 뜯어야...' .

문자열은 값 비교 연산('==')을 통해서 확인합시다.

#### 불변 객체

Google을 통해서 찾아본 바로는 frozenset, tuple 같은 불변 객체 또한 같은 id 값을 가진다고 하지만,

현재 사용하고 있는 ipad 앱 Pythonista(3.6)와 repl.it(3.8.3)으로는 확인이 안된는 상황입니다.

### 오해할 수 있는 것들

파이썬은 자동으로 GC를 통해 사용하지 않는 객체들을 수거합니다.

요점은 GC는 레퍼런스가 된 것을 GC하는 것입니다.

파이썬 코드로 레퍼렌스를 늘리는 것은 할당('=')을 하는 것입니다.

그래서 다음과 같은 코드를 작성하고 *'왜 불변 객체도, -5~256 정수도 아니고 왜 id 값이 같지'* 라고 의문을 갖을 수 있습니다.

```python
class C():
    pass
    
print(f'Fisrt C : {id(C())} <=> Second C : {id(C())}')
```

첫번째 C 인스턴스는 id 함수 내부에 이미 호출이 된 상태이고, 

id 함수가 값을 반환하면 C 인스턴스가 할당된 변수가 없으므로 메모리에 바로 해제됩니다.

두번째 C 인스턴스도 동일합니다.

그래서 두개의 C 인스턴스는 id 메서드에 잠시 쓰이고 바로 메모리에서 해제되기 때문에 같은 id 값을 가질 수 있는 것입니다.

```python
class C():
    pass
    
print(f'C() is C() : {C() is C()}') # False
```

이 경우에는 is 연산자 좌우로 C 인스턴스가 아직 평가도중에 있기 때문에 첫번째 인스턴스와 두번째 인스턴스가 공존합니다.

그래서 다른 id 값을 가지고, False를 출력합니다.

#### 잘못된 id 값 비교 연산

id 함수는 id 객체가 아닌 **int**를 반환합니다 

```python
type(id(12345)) # <class 'int'>
```

그렇기 때문에 미리 어떤 객체의 id 값을 저장하고 비교하는 경우, 

사용하는 즉시 id 값을 호출해서 혼동하는 일이 없도록 합시다.

```
num = 20
numID = id(num) # 43419980 - 실행 환경마다 다를 수 있습니다.

someNum = 20

id(someNum) is id(numID) # False, 잘못사용

id(someNum) is numID # False, num의 id 값인 '43419980'의 id 값과 비교하는 것

id(someNum) == numID # True, id값 비교하는 경우 맞지만 변수가 변할 수 있으므로 지양

id(someNum) is id(num) # False!!!
```

마지막 연산이 False인 이유는 someNum의 id값은 정수로 is 연산이 아직 안끝났으므로 아직 메모리에서 해제되지 않은 상태입니다.

num의 id 값은 id(num)을 통해 반환될 때 'id' 객체가 아닌(존재하지 않는다) 단순히 하나의 정수(int) 값으로

-5~256 범위 내의 정수가 아니기 때문에 False를 반환합니다.

그래서 id 값을 비교하기 위해서는 다음과 같이 사용해야 합니다.

```python
# 1
someNum is num

# 2
id(someNum) == id(num)
```

## 파이썬 공식 문서 - 영어(원문)

```python
id(object)
```

Return the "identity" of an object. This is an integer which is guaranteed to be unique and constant for this object during its lifetime. Two objects with non-overlapping lifetimes may have the same id() value.

CPython implementation detail: This is the address of the object in memory