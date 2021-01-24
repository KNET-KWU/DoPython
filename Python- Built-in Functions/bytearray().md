# bytearray()

## 목차

* 파이썬 공식 문서 - 한글(번역)
    
* 파이썬 공식 문서 - 영어(원문)

## 파이썬 공식 문서 - 한글(번역)

`class bytearray([source[, encoding[, errors]]])`

바이트가 담긴 새로운 배열을 생성합니다.

## 파이썬 공식 문서 - 영어(원문)

`class bytearray([source[, encoding[, errors]]])`

Return a new array of bytes.<br>
The bytearray class is a mutable sequence of integers in the range `0 <= x < 256`.<br>
It has most of the usual methods of mutable sequences, described in [Mutable Sequence Types](https://docs.python.org/3/library/stdtypes.html#typesseq-mutable), as well as most methods that the [bytes](https://docs.python.org/3/library/stdtypes.html#bytes) type has, see [Bytes and Bytearray Operations](https://docs.python.org/3/library/stdtypes.html#bytes-methods).

The optional source parameter can be used to initialize the array in a few different ways:

If it is a string, you must also give the encoding (and optionally, errors) parameters; bytearray() then converts the string to bytes using [str.encode()](https://docs.python.org/3/library/stdtypes.html#str.encode).

If it is an integer, the array will have that size and will be initialized with null bytes.

If it is an object conforming to the buffer interface, a read-only buffer of the object will be used to initialize the bytes array.

If it is an iterable, it must be an iterable of integers in the range `0 <= x < 256`, which are used as the initial contents of the array.

Without an argument, an array of size 0 is created.

See also [Binary Sequence Types -- bytes, bytearray, memoryview](https://docs.python.org/3/library/stdtypes.html#binaryseq) and [Bytearray Objects](https://docs.python.org/3/library/stdtypes.html#typebytearray).