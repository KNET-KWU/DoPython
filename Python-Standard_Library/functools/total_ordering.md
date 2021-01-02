# total_ordering

(간접적으로)정렬하는 것을 도와주는 랩퍼(Wrapper) 메소드입니다.

정렬에 필요한 사용자 정의 클래스에 정의가 안된 비교 연산자 메서드를 대신 채워줍니다.

## 목차

* 설명

<br>

## 설명

`functools.total_ordering`

비교 연산자 메서드를 하나 이상 정의한 클래스를 `total_ordering`으로 래핑하면 나머지 정렬 메서드를 정의합니다.

비교 연산자 메서드(`<`, `<=`, `>`, `>=`) 중 하나만 정의하면 됩니다

**단** 같은 값인지 확인하는 `__eq__` 연산자, `==`는 정의가 되어 있어야 합니다.

```python
__lt__ : <
__le__ : <=

__gt__ : >
__ge__ : >=

__eq__ : ==
__ne__ : !=
```

`total_ordering` 래퍼를 이용한 예시입니다 : 

```python
from functools import total_ordering

@total_ordering
class Student:
    def __init__(self, name, score):
        self.name = name
        self.score = score
        
    def __lt__(self, other):
        if not isinstance(other, Student):
            raise Exception(f'Student instance compared with : {other.__class__}')
            
        return self.score < other.score

    def __eq__(self, other):
        if not isinstance(other, Student):
            raise Exception(f'Student instance compared with : {other.__class__}')
    
        return self.score == other.score

ccppoo = Student('ccppoo', 90)
lek = Student('lek', 90)

print(ccppoo < lek)     # False   
print(ccppoo <= lek)    # True

print(ccppoo > lek)    # False
print(ccppoo >= lek)   # True 

print(ccppoo == lek)    # True
print(ccppoo != lek)    # False        
```

`total_ordering` 데코레이터가 비교 연산자 메서드를 모두 정의할 수고를 줄일 수 있지만,<br>
실행 속도에 있어서 속도 저하가 발생할 수 있으며, 함수를 래핑하기 때문에 비교 연산자로 인한 에러가 발생하는 경우, 디버깅(traceback)에 어려움을 겪을 수 있습니다.

클래스 래핑으로 인한 속도 저하가 발생한다면 6개 비교 연산자(`<`, `<=`, `>`, `>=`, `==`, `!=`)를 직접 정의하는 것이 방법입니다.