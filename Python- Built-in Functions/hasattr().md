# hasattr()

## 목차

* 파이썬 공식 문서 - 한글(번역)
    
    * assert hasattr( ... )
    
    * 값의 존재 유무, 값의 Falsey 값
    
* 파이썬 공식 문서 - 영어(원문)

## 파이썬 공식 문서 - 한글(번역)

hasattr(object, name)

첫번째 인자 *object*의 attribute 중 *name*과 일치하는 요소가 있으면 `True`, 없으면 `False`를 반환합니다.

`hasattr(object, name)`는 `getattr(object, name)` 함수를 호출해서 확인하는 것입니다.

`hasattr(object, name)`에 주어진 똑같은 두개의 인자를 `getattr(object, name)`를 호출했을 때 [AttributeError](https://docs.python.org/3/library/exceptions.html#AttributeError)가 뜨면 `False` 아니면 `True`를 반환합니다.

아래 코드와 같이 유사하게 작동합니다 - **실제 코드 아님**

```python
def hasattr(object : object, name : str) -> bool:
    try:
        getattr(object, name)
    except:
        return False
    return True
```

### assert hasattr( ... )

hasattr은 객체에 참조하고자하는 **attr**ibute가 존재하는지 확인하는 용도로 쓰입니다.

**동적 타이핑**과 덕 타이핑(duck typing)을 따르는 파이썬을 큰 규모로 사용하게 되면

수많은 클래스 타입, 객체들이 난립하게되고 실수로 타입 체크가 제대로 이루어지지 않은 경우 `AttributeError`로 고통받을 가능성이 높아집니다.

그래서 객체에 값을 불러오거나 `temp = MyObject.data`

객체에 값을 대입하거나 `MyObject.data = someData` (이 경우 에러는 발생하지 않으나 덮어쓰기가 될 수 있습니다)

객체의 메서드를 호출하기 전에 `MyObject.MyMethod() or abs(MyObject) - special method의 경우`

`hasattr(MyObject, 'data')`를 이용해 확인 후 수행합니다.

```python
# 1
if hasattr(MyObject, 'data'):
    MyObject.data = 'ccppoo'
    
# 2
if hasattr(MyObject, 'myMethod'):
    MyObject.myMethod()
```

참조하려는 객체에 반드시 특정 attribute가 있어야하는 경우, 또는 어떤 값 및 attribute가 있어야하는 경우

`assert hasattr(MyObject, value)` 처럼 빠르게 종료할 수 있습니다.

스위프트의 빠른 종료 guard let과 유사한 패턴이 떠올라서 작성해봤습니다.

```swift
// swift, 스위프트
func Func (myObject: MyObject?) {
    // nil(파이썬 None)이 주어진 경우 Func 함수 블럭 종료
    guard let data = myObject.data else { return } // 빠른 종료
    print("myObject.data : ",data)
}
```

```python
# Python, 파이썬
def Func(myObject : MyObject) -> None {
    assert hasattr(myObject, 'data')
    print(f'myObject : {myObject.data}')
```

### 값의 존재 유무, 값의 Falsey 값

이 예시는 hasattr과 **의도와 사용 방법이 유사**해서 쓰게되었습니다.

**찾고자하는 attribute**가 없는지 (2/2) + **찾고자 하는 attribute값이 Falsey** (1/2)인지 확인하는 

```python
class MyClass:
    def __init__(self, name = None):
        self.name = name
    def sayHello(self):
        # (1/2) 여기 하고
        if not self.name:
            print('Hello')
        else:
            print(f'Hello my name is {self.name}')
            
cp = MyClass('ccppoo')
no_one = MyClass()

cp.sayHello()
no_one.sayHello()

# (2/2) 여기를 봐주세요
if cp.age:
    print(f'{cp.name} age is {cp.age})
else:
    print(f'{cp.name} has no age')
```

파이썬에서 Falsey 값을 확인하기 위해서 `if ...` 에 `hasattr( ... )` 없이 접근 후 사용할 수 있습니다.

(1/2), (2/2) 경우 모두 값이 없거나, 참조하려는 **attri**bute이 없는 경우 None을 반환하기 때문에

`if not self.name`, `if cp.age`는 `if not None`, `if None`과 같이 바뀌게 되고

**~가 없는 if문** 처럼 사용할 수 있습니다.

## 파이썬 공식 문서 - 영어(원문)

hasattr(object, name)

The arguments are an object and a string.<br>
The result is `True` if the string is the name of one of the object’s attributes, `False` if not.<br>
(This is implemented by calling `getattr(object, name)` and seeing whether it raises an [AttributeError](https://docs.python.org/3/library/exceptions.html#AttributeError) or not.)