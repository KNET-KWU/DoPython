# bytes()

## 목차

* 파이썬 공식 문서 - 한글(번역)
    
* 파이썬 공식 문서 - 영어(원문)

## 파이썬 공식 문서 - 한글(번역)

`class bytes([source[, encoding[, errors]]])`

주어진 인자를 바이트 인스턴스로 반환합니다.

## 파이썬 공식 문서 - 영어(원문)

`class bytes([source[, encoding[, errors]]])`

Return a new "bytes" object, which is an immutable sequence of integers in the range `0 <= x < 256`.<br>
[bytes](https://docs.python.org/3/library/stdtypes.html#bytes) is an immutable version of 
[bytearray](https://docs.python.org/3/library/stdtypes.html#bytearray) – it has the same non-mutating methods and the same indexing and slicing behavior.

Accordingly, constructor arguments are interpreted as for [bytearray()](https://docs.python.org/3/library/stdtypes.html#bytearray).

Bytes objects can also be created with literals, see 
[String and Bytes literals](https://docs.python.org/3/reference/lexical_analysis.html#strings).

See also [Binary Sequence Types -- bytes, bytearray, memoryview](https://docs.python.org/3/library/stdtypes.html#binaryseq), 
[Bytes Objects](https://docs.python.org/3/library/stdtypes.html#typebytes), and 
[Bytes and Bytearray Operations](https://docs.python.org/3/library/stdtypes.html#bytes-methods)