# format()

## 목차

* 파이썬 공식 문서 - 한글(번역)

     보편적인 사용 방법

     * 문자열 포멧팅
     
     * 이진수, 8진수, 10진수, 16진수 변환
     
     * 실수 소수점 표현
     
     * datetime 표현
    
* 파이썬 공식 문서 - 영어(원문)

## 파이썬 공식 문서 - 한글(번역)

`format(value[, format_spec])`

첫번째 인자 *value*를 두번째 인자로 주어진 포멧 스펙(*format\_spec*) 플래그에 따라 반환합니다.

*format\_spec*은 주어진 *value*에 따라서 다르게 해석될 수 있습니다(str, int, 등 타입에 따라서).

대부분의 내장된 타입(str, int, ect)은 표준 포멧 문법이 사용됩니다 ([Format Specification Mini-Language](https://docs.python.org/3/library/string.html#formatspec)).

두번째 인자 *format\_spec*의 기본값은 빈 문자열입니다.

두번째 인자에 값이 주어지지 않으면 `str(value)`와 같이 작동됩니다. `str(value) == foramt(value)` 

<br>

`format(value, format_spec)`는 `type(value).__format__(value, format_spec)`와 같습니다. 

다시 말하자면, *value*의 타입에 매직메서드 `__format__`이 정의되어 있다면 호출이 된다는 것입니다.<br>
`__format__` 메서드를 찾는 순서는 MRO(Method Resolution Order)를 따릅니다.
 
1) 기본 내장 타입이 아닌 커스텀 타입 클래스에 `__format__`이 정의되어 있지 않은 경우 `TypeError`를 던집니다. 

2) `__format__` 메서드가 문자열(str) 타입을 반환하지 않을 경우 `TypeError`를 던집니다.


<br>

아래 내용은 보편적으로 사용하는 문자열 포멧팅에 관한 내용입니다.

자세한 내용은 다른 글에...

<br>

### 문자열 포멧팅 (변수 문자열에 쓰기)

```python
name = 'ccppoo'
age = 99

string = 'Hello my name is {} and my age is {}'.format(name, age)

print(string) # Hello my name is ccppoo and my age is 99
```

보편적으로 사용하는 문자열 포멧팅입니다.

문자열 객체 뒤에 메서드 형식으로 점 `.format`을 사용한 것을 볼 수 있듯이<br>
이런 것이 위에서 말한 기본 내장 타입에 정의된 메서드 사용 방식입니다.

중괄호(`{ }`) 안에 인덱스 없이 작성하는 경우 `format` 메서드 인자의 순서에 따라 대입되고,<br>
인덱스를 작성하는 경우 인자 대입 순서 상관없이 사용할 수 있을 뿐더러 재사용 할 수 있습니다.

```python
name = 'ccppoo'
age = 99

string = 'name is {1} my name is {1}, age is {0} my age is {0}'.format(age, name)

print(string)  # name is ccppoo my name is ccppoo, age is 99 my age is 99
```

최근 문자열 포멧팅 방법으로 **f-string**을 더 자주 사용하는 편입니다.

```python
string = f'Hello my name is {name} and my age is {age}'

print(string) # Hello my name is ccppoo and my age is 99
```

<br>

### 이진수, 8진수, 10진수, 16진수 변환

```
# 2진수
print('{:08b}'.format(255))   # 11111111

# 8진수
print('{:08o}'.format(64))   # 00000100

# 16 진수
print('0x{:02X}'.format(16))   # 0x10
```

물론 내장 함수 `bin()`, `oct()`, `hex()`를 이용하는 방법 또한 있습니다.

<br>

### 실수 소수점 표현

```python
num = 12300.123456789

print('{:10.5f}'.format(num)) # 12300.12345

print('{:10.5f}'.format(num)) # 1.23e+04
```

`:` 문자옆 점('.')을 기준으로 왼쪽은 정수 부분, 오른쪽은 소수 자릿수를 출력할 개수를 의미합니다.

<br>

### datetime 표현

시간을 사용하는 기본 내장 클래스를 사용하는 경우가 종종 있는데 `datetime` 인스턴스으로부터 원하는 형식으로 문자열을 받을 수 있습니다.

```python
import datetime
d = datetime.datetime(2021, 2, 21, 18, 33, 11)

print('{:%Y-%m-%d %H:%M:%S}'.format(d)) # '2021-02-21 18:33:11'
```

<br>

------

파이썬 버전 3.4 변경 : `object().__format__(format_spec)`이 호출될 때,<br>
즉 커스텀 타입 클래스에 `__format__`이 정의가 안되어 있고, *foramt\_spec*이 빈 문자열이 아닌 경우 `TypeError`를 던집니다.

<br>

## 파이썬 공식 문서 - 영어(원문)

`format(value[, format_spec])`

Convert a value to a "formatted" representation, as controlled by *format\_spec*.<br>
The interpretation of *format\_spec* will depend on the type of the value argument, however there is a standard formatting syntax that is used by most built-in types: [Format Specification Mini-Language](https://docs.python.org/3/library/string.html#formatspec).

The default *format\_spec* is an empty string which usually gives the same effect as calling [str(value)](https://docs.python.org/3/library/stdtypes.html#str).

A call to `format(value, format_spec)` is translated to `type(value).__format__(value, format_spec)`.<br>
`__format__(value,format_spec)` which bypasses the instance dictionary when searching for the value’s [\_\_format\_\_()](https://docs.python.org/3/reference/datamodel.html#object.__format__) method.<br>
A [TypeError](https://docs.python.org/3/library/exceptions.html#TypeError) exception is raised if the method search reaches [object](https://docs.python.org/3/library/functions.html#object) and the *format\_spec* is non-empty, or if either the *format\_spec* or the return value are not strings.

------

Changed in version 3.4: `object().__format__(format_spec)` raises [TypeError](https://docs.python.org/3/library/exceptions.html#TypeError) if *format\_spec* is not an empty string.