# repr()

repr은 representation의 줄임말로 `묘사하다, 대표하다` 의미를 갖고 있습니다.

## 목차

* 파이썬 공식 문서 - 한글(번역)

    * \_\_repr\_\_() 직접 구현해보기

    * \_\_repr\_\_() 재정의 하기

* 파이썬 공식 문서 - 영어(원문)

## 파이썬 공식 문서 - 한글(번역)

repr(object)

Return a string containing a printable representation of an object. 

객체에 대한 표현을 문자열로 반환합니다.

문자열을 반환하지 않은 경우 TypeError을 던집니다.

repr 함수는 디버깅할 때 사용하므로, 세부적인 정보를 제공하는 것이 중요합니다.

클래스를 작성할 때 \_\_str\_\_ 를 정의하지 않아도 repr 함수가 정의되어있으면 repr 함수값을 반환합니다.

```python
name = 'ccppoo'

# 문자열(str)의 경우 문자열 값 그대로 출력합니다
print(name.__repr__()) # 'ccppoo'
print(name) # 'ccppoo'
```

For many types, this function makes an attempt to return a string that would yield an object with the same value when passed to eval(), otherwise the representation is a string enclosed in angle brackets that contains the name of the type of the object together with additional information often including the name and address of the object. 

대부분의 경우, ``__repr__()`` 는 객체를 `eval()` 함수에 인자로 넣을 때 반환하는 문자열과 동일하게 반환하려고 시도합니다.

그 외에 객체에 대한 표현(representation)으로 객체의 타입의 이름과 메모리 주소를 꺾쇠('<' , '>')로 감싼 문자열을 반환합니다.

```python
class C: pass
c = C()

# 네임스페이스(현재 스크립트 ;__main__)과 객체 이름, 그리고 메모리 주소를 출력합니다 (딱히 의미는 없습니다)
print(c) # <__main__.C object at 0x114e0e588> - 메모리 주소는 실행할 때마다 매번 바뀔 수 있습니다
```

객체를 그냥 출력하는 경우 뒤에 있는 메모리 주소를 정수(int)로 바꿔볼까요?

```python
class C:
    pass
    
c = C()

print(f'Just print c : {c}') 

print(f'str(c) : {str(c)}')

memAddress = c.__repr__().split()[-1][:-1] # 메모리 주소만 다로 분리

print(f'memAddress : {memAddress}')

# 16진수를 정수로 변환
print('int(memAddress, 16) :', int(memAddress, 16))

print(f'id of c : {id(c)}')

>>> str(c) : <__main__.C object at 0x114fd2908>
>>> Just print c : <__main__.C object at 0x114fd2908>
>>> memAddress : 0x114fd2908
>>> int(memAddress, 16) : 4647102728
>>> id of c : 4647102728
```

`__repr__`에서 반환한 메모리 주소 값의 10진수가  id() 함수의 반환값과 같네요!

이로써 어쩌다보니 id() 값이 진짜 메모리 주소인 것을 알 수 있었습니다.

클래스를 정의할 때 \_\_repr\_\_() 메서드를 재정의 해서 원하는 문자열을 출력할 수 있게끔 할 수 있습니다.

### \_\_repr\_\_() 직접 구현해보기

\_\_repr\_\_()에 필요한 건 객체 타입의 이름과 메모리 주소인 것을 봤습니다.

둘다 충분히 구할 수 있으니 직접 구현해보죠!

```python
# __repr__() 구현해보기

class C:
    def __repr__(self) -> str:
        # type 함수를 통해서 인스턴스의 이름을 받습니다
        objTypeName = str(type(self))[1:-1]
        
        # 여기서 필요한 문자열만 뽑아보죠
        objTypeName = objTypeName.replace('class', '').replace('\'','').strip()
        
        # 인스턴스의 16진수 메모리 주소는 id() 함수를 통해 구합니다
        return f'<{objTypeName} object at {hex(id(self))}>'

class D:
    pass

ccppoo = C()

dummy = D()

print(ccppoo)

print(dummy)

>>> <__main__.C object at 0x11623ad30>
>>> <__main__.D object at 0x11623a7f0>
```

### \_\_repr\_\_() 재정의 하기

```
# __repr__를 재정의하지 않고 객체를 출력한 결과

<__main__.C object at 0x11623ad30>
```

여러분은 위 결과를 보고 객체에 대한 정보를 한눈에 알 수 있겠나요?

대략적인 정보로 실행한 스크립트(\_\_main\_\_)에 정의한 클래스 이름 'C'의 한 인스턴스라는 것은 알 수 있지만

더이상 정보를 알 수 없습니다.

한두개의 인스턴스만 생성되었다면, 어떤 인스턴스인지 알 수 있겠지만 수가 많아지면 어떤 인스턴스를 출력했는지 알 수 없겠죠? ㅇㅈ?

```python
class Person:
    def __init__(self, name: str, age: int):
        self.name = name
        self.age = age

    def __repr__(self):
        path, fileName = __file__, ''
        
        if '/' in path:
            fileName = path.split('/')[-1]
        elif '₩' in path:
            fileName = path.split('₩')[-1]
        
        log = f'Instance of class "Person" \ndefined in module: {fileName}'
        sepConsole = '=-='*5
        
        return f'{sepConsole}\n{log}\nname: {self.name}\nage: {self.age}\n{sepConsole}'
        
ccppoo = Person('ccppoo', 99)

print(ccppoo)

###### 출력 결과 ######
# =-==-==-==-==-=
# Instance of class "Person" 
# defined in module: tt.py
# name: ccppoo
# age: 99
# =-==-==-==-==-=
#####################
```

정신없긴 하지만, 객체에 대한 정보를 보여주니 알아보기 편합니다.

실제 클래스를 작성할 때는 알아볼 수 있게끔 수정하시면 됩니다.

## 파이썬 공식 문서 - 영어(원문)

repr(object)

Return a string containing a printable representation of an object.<br>
For many types, this function makes an attempt to return a string<br>
that would yield an object with the same value when passed to eval(),<br> 
otherwise the representation is a string enclosed in angle brackets<br>
that contains the name of the type of the object together<br>
with additional information often including the name and address of the object.<br>
A class can control what this function returns for its instances by defining a __repr__() method.