# struct - 구조체

파이썬의 `struct` 모듈은 C언어 수준의 바이트 단위로 조작하는 구조체(struct)를 사용할 수 있게 해줍니다.

이진 데이터의 형태로 다루는 **네트워크**, **파일**, 등 다양한 분야에서 사용할 수 있습니다.

<!-- 이거 뭔지 설명하기 !!!!!!! -->
C의 구조체와 파이썬 값 타입과 C 값 타입으로 변환을 설명하는 설명문은 [Format Strings](https://docs.python.org/3/library/struct.html#struct-format-strings)를 사용합니다.

## 목차

* struct 클래스

    생성자

    * struct.Struct(format)

    메서드, Method
    
    * pack(v1, v2, ...)
    
    * pack_into(buffer, offset, v1, v2, ...)
    
    * unpack(buffer)
    
    * unpack_from(buffer, offset=0)
    
    * iter_unpack(buffer)

* struct 모듈 함수

    * pack(format, v1, v2, ...)
    
    * pack_into(format, buffer, offset, v1, v2, ...)
    
    * unpack(format, buffer)
    
    * unpack_from(format, /, buffer, offset=0)
    
    * iter_unpack(format, buffer)
    
    * calcsize(format)

* 에러

    * exception struct.error

## struct 클래스

같은 형식의 구조체를 반복적으로 읽고/써야하는 경우,

구조체의 형식인 *포멧(format)*을 인자로 받아 들이는 생성자를 이용한 인스턴스를 사용하는 것이 더 효율적입니다.

```python
# 네트워크로 이용할 경우
import strcut

# 이런식으로 반복적으로 사용하는 것보다
def readStream(buff : Buffer):
    structData = struct.unpack(<format>, buff)
    return structData

# 이렇게 사용하는 것이 더 효율적 입니다.
streamReader = struct.Struct()

def readStream(buff : Buffer)
    return streamReader.unpack(buff)
```

`Struct` 클래스 생성자의 인자로 `format`를 미리 지정했기 때문에 `struct` 함수와 메서드 차이는 첫번째 인자로 *format*의 유무입니다.

이점 외에는 똑같이 작동합니다.

### struct.Struct(format)

*format*을 인자로 받습니다

```python
from struct import *

# int 2개, 크기 10인 char[](배열)
myFormat = Struct('2i10s') 
```

생성자에 주어진 *format* 속성은 **읽기 전용**이며, *size*를 통해 구조체 크기를 읽을 수 있습니다.

```python
print(myFormat.format)  # b'2i10s'
print(myFormat.size)    # 18 (4+4 + 10)
```

### pack(v1, v2, ...)

`struct.pack`과 똑같이 동작합니다.

반환하는 값의 길이(`len()`)은 구조체의 크기(`Struct.size`)와 같습니다.

```python
result = myFormat.pack(10, 20, b'ccppoo')

print(result)       # b'\n\x00\x00\x00\x14\x00\x00\x00ccppoo\x00\x00\x00\x00'
print(len(result))  # 18
```

### pack_into(buffer, offset, v1, v2, ...)

첫번째 인자 *buffer*에 포멧에 맞춰 두번째 인자  *offset*부터 값을 대입합니다.

이미 생성된 버퍼 또는 구조체에 대입이 안된 값을 대입하는 것이므로 **몇 번째 바이트**부터 저장을 할지 포멧에 맞춰<br>
두번째 인자 *offset*를 대입해야 합니다. 

`pack` 된 `struct` 객체는 읽기 전용이므로 C 타입의 String Buffer를 사용해야합니다.<br>
읽기/쓰기 가능한 바이트 객체 중에서는 파이썬의 기본 데이터 타입인 `bytearray`를 사용해도 됩니다.

```python
from struct import *
import ctypes

# int 2개, 크기 10인 char[](배열)
myFormat = Struct('2i10s')

st = ctypes.create_string_buffer(myFormat.size + 8)

#st = bytearray(40)

myFormat.pack_into(st, 3, 10, 20, b'hello')

print(st.raw)   # b'\x00\x00\x00\n\x00\x00\x00\x14\x00\x00\x00hello\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00'
```

### unpack(buffer)

`struct` 형태의 바이트 객체 또는 파이썬 코드 수준에서 생성된 바이트 객체를 포멧에 맞춰 언패킹합니다.

포멧에 존재하는 값의 개수의 길이를 가진 튜플을 반환합니다.

언패킹하는 버퍼의 크기는 **반드시** 포멧의 크기(`calcsize()` 메서드의 반환값)와 일치해야합니다.

```python
from struct import *
import ctypes

# int 2개, 크기 10인 char[](배열)
myFormat = Struct('2i10s')

st= myFormat.pack(30, 60, b'hello')

data = myFormat.unpack(st)

print(data) # (30, 60, b'hello\x00\x00\x00\x00\x00')
```

### unpack_from(buffer, offset=0)

`struct` 형태의 바이트 객체 또는 파이썬 코드 수준에서 생성된 바이트 객체를 포멧에 맞춰 언패킹합니다.

다만, 두번째 인자 *offset* 만큼 떨어진 바이트 지점부터 포멧의 크기 만큼 언패킹합니다. 

언패킹하는 버퍼의 크기는 **반드시** 포멧의 크기(`calcsize()` 메서드의 반환값)와 일치해야합니다.

```python
from struct import *
import ctypes

myFormat = Struct('2i10s')

buff = ctypes.create_string_buffer(myFormat.size + 10)

# 버퍼에서 5 바이트 뒤로 저장합니다.
myFormat.pack_into(buff, 5, 33, 55, b'ccppoo')

data = myFormat.unpack_from(buff, 5)

print(data) # (33, 55, b'ccppoo\x00\x00\x00\x00')
```

### iter_unpack(buffer)

두번쨰 인자 *buffer*를 첫번째 인자 *format*의 크기만큼 반복적으로 언패킹한 **소모하는** 이터레이터를 반환합니다.

주어진 버퍼는 *format*의 크기만큼 반복해서 읽어들이며, 읽어들인 바이트가 포멧에 맞지 않는 경우 에러를 던집니다.

```python
myData = bytes()

for i in range(3):
    buff = pack(myFormat.format, i, i+10, b'ccppoo')
    myData += buff
    
iters = iter_unpack(myFormat.format, myData)

for item in iters:
    print(item)
```

출력 결과

```python
(0, 10, b'ccppoo\x00\x00\x00\x00')
(1, 11, b'ccppoo\x00\x00\x00\x00')
(2, 12, b'ccppoo\x00\x00\x00\x00')
```

-------

## struct 모듈 함수

### pack(format, v1, v2, ...)

주어진 *format*에 맞춰 주어진 인자 *v1*, *v2*, ... 를 구조체로 반환합니다.

```python
import struct

myFormat = '2i10s'
st = struct.pack(myFormat, 10, 20, b'ccppoo')
print(st)   # b'\n\x00\x00\x00\x14\x00\x00\x00ccppoo\x00\x00\x00\x00'
```

### pack_into(format, buffer, offset, v1, v2, ...)

두번째 인자 *buffer*에 첫번째 인자 *format*에 맞춰 두번째 인자 *offset*부터 값을 대입합니다.

이미 생성된 버퍼 또는 구조체에 대입이 안된 값을 대입하는 것이므로 **몇 번째 바이트**부터 저장을 할지 포멧에 맞춰<br>
세번째 인자 *offset*를 대입해야 합니다. 

`pack` 된 `struct` 객체는 읽기 전용이므로 C 타입의 String Buffer를 사용해야합니다.<br>
읽기/쓰기 가능한 바이트 객체 중에서는 파이썬의 기본 데이터 타입인 `bytearray`를 사용해도 됩니다.

```python
from struct import *
import ctypes

st = ctypes.create_string_buffer(myFormat.size + 8)

#st = bytearray(40)

pack_into('2i10s', st, 3, 10, 20, b'hello')

print(st.raw)   # b'\x00\x00\x00\n\x00\x00\x00\x14\x00\x00\x00hello\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00'
```

### unpack(format, buffer)

`struct` 형태의 바이트 객체 또는 파이썬 코드 수준에서 생성된 바이트 객체를 포멧에 맞춰 언패킹합니다.

포멧에 존재하는 값의 개수의 길이를 가진 튜플을 반환합니다.

언패킹하는 버퍼의 크기는 **반드시** 포멧의 크기(`calcsize()` 메서드의 반환값)와 일치해야합니다.

```python
from struct import *

st = pack('2i6c', 10, 20, b'ccppoo')

print( unpack('2i6c', st))  # (10, 20, b'ccppoo')
```

주어진 포멧 형식을 일치하는 `struct` 모듈을 통해 만들어지지 않은 바이트 객체 또한 사용할 수 있습니다

```python
from struct import *
import ctypes

st= pack('2i10s', 30, 60, b'hello')

data = unpack('2i10s', st)

print(data) # (30, 60, b'hello\x00\x00\x00\x00\x00')
```

### unpack_from(format, /, buffer, offset=0)

`struct` 형태의 바이트 객체 또는 파이썬 코드 수준에서 생성된 바이트 객체를 포멧에 맞춰 언패킹합니다.

다만, 두번째 인자 *offset* 만큼 떨어진 바이트 지점부터 포멧의 크기 만큼 언패킹합니다. 

언패킹하는 버퍼의 크기는 **반드시** 포멧의 크기(`calcsize()` 메서드의 반환값)와 일치해야합니다.

```python
from struct import *
import ctypes

buff = ctypes.create_string_buffer(myFormat.size + 10)

buff = pack_into('2i10s', buff, 5, 33, 55, b'ccppoo')

data = unpack_from('2i10s', buff, 5)

print(data) # (33, 55, b'ccppoo\x00\x00\x00\x00')
```

### iter_unpack(format, buffer)

두번쨰 인자 *buffer*를 첫번째 인자 *format*의 크기만큼 반복적으로 언패킹한 **소모하는** 이터레이터를 반환합니다.

주어진 버퍼는 *format*의 크기만큼 반복해서 읽어들이며, 읽어들인 바이트가 포멧에 맞지 않는 경우 에러를 던집니다.

```python
from struct import *

# int 2개 있는 간단한 포멧
fmt = '2i'

myData = bytes()

for i in range(3):
    buff = pack(fmt, i, i+10)
    myData += buff
    
iters = iter_unpack(fmt, myData)

for item in iters:
    print(item)
```

출력결과

```python
(0, 10)
(1, 11)
(2, 12
```

### calcsize(format)

주어진 포멧의 크기를 반환합니다.

포멧의 크기는 C언어의 데이터 타입에 따른 바이트 크기를 의미합니다.

**파이썬이 실행되는 환경에 따라 크기는 변할 수 있습니다** ; 32-bit, 64-bit, OS 종류별로

```python
from struct import *

# long을 의미하는 l
print(calcsize('l'))    # 8

# short를 의미하는 h
calcsize('h')   # 2

# char(char [])를 의미하는 c(s)
calcsize('s')   # 1
```