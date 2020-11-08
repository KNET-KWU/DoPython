# ascii()

ASCII - American Standard Code for Information Interchange (미국 정보 교환 표준 부호)

아스키문자가 아닌 문자들을 아스키문자로 바꿔주는 메서드 `ascii()`에 대해서 알아봅시다.

## 목차

* 파이썬 공식 문서 - 한글(번역)

    * 클래스를 정의할 때 repr()를 되도록 정의하자

    * ascii()의 작동하는 방식(예시 코드)
    
    * ascii()와 chr(), ord()
    
        * ascii() 구현해보기
        
* 파이썬 공식 문서 - 영어(원문)

## 파이썬 공식 문서 - 한글(번역)

```python
ascii(object)
```

`ascii(object)` 메서드 인자로 문자열이 아닌 객체가 주어진 경우 출력 가능한 문자열을 반환하는 [repr()](https://docs.python.org/3/library/functions.html#repr) 메서드를 호출합니다.<br>
문자열에 아스키(ASCII) 코드가 아닌 문자(영어, 숫자, 기호 외)가 포함되어 있으면 \\x, \\u \\U 이스케이프 문자로 시작하는 단어로 바뀐 문자로 변환한 문자열을 반환합니다.

간단히 말하면 아스키코드만으로 나타낼 수 있는 문자의 **유니코드 값**으로 바꾼뒤 반환하는 것입니다

* 이 문자가 아스키코드에 포함되어 있다? -> 그대로 출력

* 이 문자가 아스키코드에 포함이 안되어 있다? -> 아스키코드를 이용해 표현할 수 있는 유니코드 값으로 바꾼뒤 출력한다

### 클래스를 정의할 때 repr()를 되도록 정의하자

이번 글에서 `ascii(object)` 메서드를 사용하기 위해서만이 아니라 평상시에 객체에 대한 정의를 한눈에 볼 수 있도록

`repr()` 메서드를 정의하는 습관을 가지고 있는 것이 좋습니다 (`repr().md`도 읽어보세요!).

디버깅 또는 로깅을 할 때 `print(Member.name, Member.age)`처럼 직접 참조하면 나중에 언젠가 RefferenceError로 고생할게 뻔하기 때문입니다.

**아래 예시는 회원(Member) 객체에 담긴 데이터를 ASCII 포맷으로 저장할 수 있는 데이터베이스(DB)에 저장할 수 있겠끔 `ascii()` 메서드를 호출하는 상황을 가정하에 만들었습니다**.

repr()를 재정의하지 **않은** 클래스:

```python
class Member:
    def __init__(self, nameENG, nameKOR):
        self.nameENG = nameENG
        self.nameKOR = nameKOR
      
member = Member('ccppoo', '씨포')

ascii(member)   # <__main__.Member object at 0x11ea6e7b8>
```

repr()를 재정의한 클래스:

```python
class Member:
    def __init__(self, nameENG, nameKOR):
        self.nameENG = nameENG
        self.nameKOR = nameKOR
      
    def __repr__(self) -> str:
        return f'{self.nameENG}, {self.nameKOR}'
         
member = Member('ccppoo', '씨포')

ascii(member)   # ccppoo, \uc528\ud3ec
```

그럼 *\\uc528\\ud3ec* 이렇게 생긴 문자열은 나중에 어떻게 DB에서 꺼내서 언제 또 한글로 바뀌냐고요?

유니코드에 속해있는 문자를 의미하는 이스케이프 문자(\\x, \\u, \\U)가 이미 있기 때문에 파이썬이 알아서 출력해줍니다.

우리가 평소에 일반적으로 쓰는 줄바꿈(엔터)을 의미하는 **\\n**, 탭(Tab)을 의미하는 **\\t**와 같은 개념입니다.

```python
print('\uc528\ud3ec')   # 씨포
```

그러면 이스케이프 문자만 쓰는다면?

```python
print('\u') # SyntaxError: (unicode error) 'unicodeescape' codec can't decode bytes in position 0-1: truncated \uXXXX escape
```

유니코드임을 암시하는 prefix(\\x, \\u, \\U)가 있지만 디코딩할 데이터가 없으니 에러를 던집니다.

### ascii()의 작동하는 방식(예시 코드)

```python
def ascii(object) -> str :

    # 기존 문자열
    string = object
    
    # 기존 문자열에서 아스키 문자로 모두 바꾼 문자열
    asciiString = str()

    if object.__class__ is not str:
        string = repr(object)

    for char in string:
        if char is not ASCIIcode:
            asciiString.append(toASCIIcode(char))
        else:
            asciiString.append(char)
    
    return asciiString
```

### ascii()와 chr(), ord()

`ascii()`와 **비슷**한 메서드가 있다면 `ord()` 메서드,

`ascii()`와 **비슷**하게 반대로 작동하는 메서드가 있다면 `chr()` 메서드가 있습니다.

차이가 있다면 `ascii()`는 **하나의 문자**가 아닌 **문자열**을 다룬다는 것입니다.

\\x, \\u, \\U로 시작하는 유니코드는 사전으로 정해져있으니 현장에서 ascii를 직접 구현하는 끔찍한 행위는 하지맙시다.

비슷하면서도 안비슷한 유니코드 메서드들...

```python
ascii('가')  # "'\\uac00'"

ord('가')    # 44032

hex(44032)  # 0xac00 -> 아스키 코드 값은 \u 이스케이프 문자와 16진수 부분을 합친것이네요

#########

ascii('å')  # "'\\xe5'"

ord('å')    # 229

hex('å')    # '0xe5' -> 아스키 코드 값은 \x 이스케이프 문자와 16진수 부분을 합친것이네요 
```

위에 본 예시처럼 '가'와 'å' 두 문자 둘다 문자값의 16진수 형태를 이용하는 것은 같지만, 이스케이프 문자가 다르다는 것을 알 수 있습니다.

#### ascii() 구현해보기

이 예시는 모든 유니코드 문자가 아닌 **\u** 이스케이프 문자로 시작하는 한글에 쓸 수 있는 부분으로 구현했습니다.

```python
class Member:
    def __init__(self, nameENG, nameKOR):
        self.nameENG = nameENG
        self.nameKOR = nameKOR
      
    def __repr__(self) -> str:
        return f'{self.nameENG}, {self.nameKOR}'

    def myAscii(self) -> str:
        
        asciiString = ''
        
        for char in self.__repr__():
            # 아스키 코드는 0~255 사이의 정수 값을 가지고 있으므로 
            # ord(char)가 255 이하인 경우 ascii 문자이므로 그대로 추가한다.
            if ord(char) < 256:
                asciiString += char
            else:
                # 유니코드 형태에 맞게 수정
                asciiString += '\\u' + format(ord(char), 'x')
                
        return asciiString
        
member = Member('ccppoo', '씨포')
        
ascii(member)    # ccppoo, \uc528\ud3ec

member.myAscii() # ccppoo, \uc528\ud3ec
```

## 파이썬 공식 문서 - 영어(원문)

```python
ascii(object)
```

As [repr()](https://docs.python.org/3/library/functions.html#repr), return a string containing a printable representation of an object, but escape the non-ASCII characters in the string returned by [repr()](https://docs.python.org/3/library/functions.html#repr) using \x, \u or \U escapes.<br>
This generates a string similar to that returned by [repr()](https://docs.python.org/3/library/functions.html#repr) in Python 2.