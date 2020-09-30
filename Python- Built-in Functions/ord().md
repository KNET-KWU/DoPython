# ord()

## 목차

* 파이썬 공식 문서 - 한글(번역)
    
* 파이썬 공식 문서 - 영어(원문)

## 파이썬 공식 문서 - 한글(번역)

ord(c)

Given a string representing one Unicode character, return an integer representing the Unicode code point of that character.<br>

하나의 유니코드로 표현될 수 있는 하나의 문자(길이가 1인 문자열)을 인자로 대입하면, 해당 문자의 유니코드 포인트(정수)를 반환합니다.

```python

ord('a')    # 97
ord('€')    # 8364
ord('가')    # 44032
```

ord() 메서드는 chr() 메서드와 정반대로 작동합니다.

```python
chr(ord('a'))   # a
chr(ord('€'))   # €
chr(ord('가'))   # 가
```

유니코드와 관련된 내용은 chr().md에 작성되어있습니다.

참고하세요!

## 파이썬 공식 문서 - 영어(원문)

ord(c)

Given a string representing one Unicode character, return an integer representing the Unicode code point of that character.<br>
For example, `ord('a')` returns the integer `97` and `ord('€')` (Euro sign) returns `8364`.<br>
This is the inverse of [chr()](https://docs.python.org/3/library/functions.html#chr)