# hash()

모든 자료를 하나의 정수타입으로 구분할 수 있는 방법이자 메서드

## 목차

* 파이썬 공식 문서 - 한글(번역)
    
    * 내가 만든 타입 hash 따로 정의하고 싶다면
    
    * 타입은 다르지만 값이 같은 숫자 자료형의 해시값의  경우

    * 딕셔너리(dict)에 쓰이는 hash 메서드
    
* 파이썬 공식 문서 - 영어(원문)

## 파이썬 공식 문서 - 한글(번역)

`hash(object)`

첫번째 인자로 주어진 객체에 대한 해시(hash)값이 존재하는 경우 해시값을 반환합니다.

```python
hash('ccppoo')  # 6630617468109008276
hash('ccppoo1') # 8080161935219801078
```

`__hash__`에 정의되어 반환되는 해시값은 반드시 정수(Integers)이여야 합니다.

## 내가 만든 타입 hash 따로 정의하고 싶다면

MD5 같이 **숫자와 문자가 있는** 해시값을 사용해야하는 경우, 매직메서드 형태(\_\_hash\_\_)가 아닌 일반 메서드 형태로 정의하신 뒤 사용하셔야합니다.

```python
class MyClass:
    # 1번
    def __hash__(self):
        return '2a12b93'
    
    # 2번    
    def hash(self) -> str:
        return '2a12b93'        

myClass = MyClass()

# 1번 '__hash__'로 정의된 메서드가 호출됩니다. 정수가 아닌 문자열을 반환해서 타입 에러를 던집니다
hash(myClass) # TypeError: __hash__ method should return an integer

# 2번 'hash'로 정의된 메서드가 호출됩니다.
myClass.hash()  # '2a12b93'
```

참고로 `__hash__`와 같은 대부분의 내장함수는 C로 작성되어 있기 때문에 재정의를 하지 못합니다.

## 딕셔너리(dict)에 쓰이는 hash 메서드

`hash()`는 딕셔너리의 룩업(look up)을 할 때 키값으로 사용됩니다.

즉, 딕셔너리 아이템들의 키값들은 하나의 헤시 테이블('hash table') 형태로 존재하고 참조를 할 때 사용된다는 의미입니다.

```python
class MyClass:
    pass
    
    
class MyHashClass:
    def __hash__(self):
        return 'an12oan1'
        
mc1 = MyClass()        
myDict = {mc1 : 1}  # 아무 문제 없이 딕셔너리 객체가 만들어집니다, 커스텀 타입(클래스)도 키값으로 사용할 수 있습니다

mh1 = MyHashClass()
myDict2 = {mh1 : 1} # TypeError: __hash__ method should return an integer
```

키값으로 객체가 사용되었을 때 해시값을 사용한다고 볼 수 있는 예시입니다.

`myDict2`는 딕셔너리 객체가 생성된 후 키값을 만들 때 `__hash__`가 정수(int)를 반환해야하는 조건을 만족시키지 않아 에러를 던진 것을 보실 수 있습니다.

<br>

## 타입은 다르지만 값이 같은 숫자 자료형의 경우

숫자 자료형의 경우 값이 같은 경우('==' 값 비교 연산을 했을 때;`True`를 반환하는 경우) 해시값이 같습니다.

```
hash(1)     # 1
hash(1.0)   # 1
hash(complex(1))    # 1

# 파이썬의 bool은 int 타입을 상속받아 만들어진 타입으로 정수와 같이 해시값을 가질 수 있습니다 
hash(True)  # 1
```

------

**참고** : 따로 정의한 `__hash__` 메서드의 반환값의 길이가 너무 긴 경우, 구동되고 있는 기계의 비트 대역폭(32, 64)에 따라 길이가 줄어들 수 있습니다.

덧, 클래스 타입에 따라 해시하는 방법이 달라서 설명하는 건 안쓰기로 했습니다.

## 파이썬 공식 문서 - 영어(원문)

`hash(object)`

Return the hash value of the object (if it has one).<br>
Hash values are integers.<br>
They are used to quickly compare dictionary keys during a dictionary lookup.<br>
Numeric values that compare equal have the same hash value (even if they are of different types, as is the case for 1 and 1.0).


**Note**: For objects with custom [\_\_hash\_\_()](https://docs.python.org/3/reference/datamodel.html#object.__hash__) methods, note that [hash()](https://docs.python.org/3/library/functions.html#hash) truncates the return value based on the bit width of the host machine. See [\_\_hash\_\_()](https://docs.python.org/3/reference/datamodel.html#object.__hash__) for details.