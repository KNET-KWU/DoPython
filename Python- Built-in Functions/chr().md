# chr()

## 목차

* 파이썬 공식 문서 - 한글(번역)

    * 정수로 한글 써보기 (한글 유니코드)

* 파이썬 공식 문서 - 영어(원문)

## 파이썬 공식 문서 - 한글(번역)

chr(i)

유니코드 포인트가 인자로 주어진 정수 *i*에 해당하는 문자열을 반환합니다.

```python
chr(97) # 'a'

chr(8364) # '€"
```

chr() 메서드는 인자로 하나의 문자를 유니코드 포인트를 반환하는 ord() 메서드의 정반대의 역할을 하는 메서드입니다.

인자에 넣을 수 있는 정수의 유효범위는 0부터 1,114,111 입니다.<br>
(최댓값 : 0x10FFFF, 16진수)

최댓값을 초과한 정수를 인자로 넣은경우 ValueError를 던집니다.

```python
chr(1114111) # 􏿿 - 1114111에 해당하는 유니코드가 없기 때문에 없는 문자로 출력됩니다.
chr(0x10FFFF) # 􏿿

chr(1114112) # ValueError: chr() arg not in range(0x110000)
chr(0x110000) # ValueError: chr() arg not in range(0x110000)
```

### 정수로 한글 써보기 (한글 유니코드)

한글의 유니코드는 44032~55203까지 할당되어 있다.

55203 - 44032 + 1 = 11,172

현대 한국어의 자모 조합으로 만든 한글 11,172자를 chr()메서드를 이용해 표현할 수 있다.

```python
chr(44032) == '가' # True
```

## 파이썬 공식 문서 - 영어(원문)

chr(i)

Return the string representing a character whose Unicode code point is the integer *i*.<br>
For example, `chr(97)` returns the string `'a'`, while `chr(8364)` returns the string `'€'`.<br>
This is the inverse of `ord()`.

The valid range for the argument is from 0 through 1,114,111 (0x10FFFF in base 16).<br>
ValueError will be raised if *i* is outside that range