# Binary Sequence Types - Memoryview, 메모리 뷰

구조체 문자열 -> b' ... ',  d' ... ', I' ..."

memoryview objects allow Python code to access the internal data of an object that supports the buffer protocol without copying.

메모리 뷰 객체는 파이썬 코드를 자체만으로 버퍼 프로토콜을 지원하는 객체의 데이터 내부를 접근할 수 있습니다.

```python
class memoryview(obj)
```

Create a memoryview that references obj. obj must support the buffer protocol.<br> Built-in objects that support the buffer protocol include bytes and bytearray.

객체 obj를 참조하는 메모리 뷰를 생성하는 코드입니다.<br>
인자로 주어진 obj는 반드시 버퍼 프로토콜을 지원해야합니다.<br>
버퍼 프로토콜을 지원하는 내장 객체 중에는 bytes와 bytearray가 있습니다.

A memoryview has the notion of an element, which is the atomic memory unit handled by the originating object obj.<br> For many simple types such as bytes and bytearray, an element is a single byte, but other types such as array.array may have bigger elements.

!!!!!!!!!!
메모리 뷰는 객체 요소에 대한 '개념'을 보여준다고 할 수 있는데, atomic memory unit을 점유하고 있는 객체를 의미합니다.

len(view) is equal to the length of tolist.<br> If view.ndim = 0, the length is 1.<br> If view.ndim = 1, the length is equal to the number of elements in the view.<br> For higher dimensions, the length is equal to the length of the nested list representation of the view.<br> The itemsize attribute will give you the number of bytes in a single element.

len(view)는 tolist의 길이와 같습니다.<br>
view.ndim == 0 인 경우, 길이는 1입니다.<br>
view.ndim == 1 인 경우, 메모리 뷰에 존재하는 요소들의 개수와 동일합니다.<br>
view.ndim >= 2 인 경우, 길이는 뷰에 내부에 존재하는 리스트의 개수와 동일합니다.<br>
itemsize 요소는 하나의 요소에 있는 바이트 개수를 의미합니다.

```python
memview.ndim == 0 인 경우와 memview.ndim == 2 인 경우 예시???
```

A memoryview supports slicing and indexing to expose its data.<br> One-dimensional slicing will result in a subview:

메모리 뷰는 일반 리스트와 같이 슬라이싱(slicing)과 인덱싱(indexing)을 지원합니다.<br>
일차원 슬라이싱(One-dimensional slicing)은 다른 메모리 뷰 객체를 반환합니다.

```python
memv = memoryview(b'123456789')
memv[0] # 49 -> 숫자 1의 아스키 코드 값은 '49'

memv[2] = 'k' # TypeError : cannot modify read-only memory
```

If format is one of the native format specifiers from the struct module, indexing with an integer or a tuple of integers is also supported and returns a single element with the correct type.<br>
One-dimensional memoryviews can be indexed with an integer or a one-integer tuple.<br>
Multi-dimensional memoryviews can be indexed with tuples of exactly ndim integers where ndim is the number of dimensions. <br>
Zero-dimensional memoryviews can be indexed with the empty tuple.

메모리 뷰를 만드려는 대상이 파이썬의 내장 모듈 'struct'에서 확장된 또다른 파이썬 내장 타입인 경우,
일반 리스트처럼 슬라이싱을 할 수 있습니다.<br>
일차원 메모리 뷰는 하나의 정수 또는 하나의 정수만 있는 튜플로 인덱싱이 가능합니다.<br>
N-차원 메모리 뷰는 N개의 정수 또는 N개의 정수가 있는 튜플로 인덱싱이 가능합니다.

Here is an example with a non-byte format:

```python
import array

a = array.array('l', [1,2,3,4,5])

m = memoryview(a)

m[0]  # 1
m[-1] # 5

m[0:3].tolist() # [1,2,3]
```

If the underlying object is writable, the memoryview supports one-dimensional slice assignment. Resizing is not allowed:

메모리 뷰의 대상 객체가 쓰기가능(writable)이면, 메모리 뷰는 일차원 슬라이싱을 지원하지만 크기를 수정하는 것은 지원하지 않습니다.

```python
data = bytearray(b'123456')

mem = memoryview(data)

mem.readonly # False

mem[3:5] = b'hi'
mem.obj # bytearray(b'123hi6')

# 수정할 바이트의 길이와 쓰려고 하는 데이터의 길이가 다른 경우 에러를 던집니다.
mem[3:5] = b'Hello' # ValueError: memoryview assignment: lvalue and rvalue have different structures

# 수정할 바이트의 길이보다 쓰려고 하는 데이터 길이가 작아도 에러를 던집니다.
mem[3:5] = b'h' # ValueError
```

One-dimensional memoryviews of hashable (read-only) types with formats ‘B’, ‘b’ or ‘c’ are also hashable.<br> The hash is defined as hash(m) == hash(m.tobytes()):

해시 함수를 사용할 수 있는(read-only) 타입 'B', 'b', 'c'으로 된 일차원 메모리 뷰 또한 해시 함수를 사용할 수 있습니다.

참고로 해시 함수 자체의 의미가 다음과 같습니다: hash(obj) == hash(obj.tobytes() )

```python

m1 = memoryview(b'abcde')
hash(m1) == hash(b'abcde') # True

m1 = memoryview(b'abcde')
m2 = memoryview(b'abcde12345')

hash(m1) == hash(m2[:5]) # True
```

파이썬 버전 3.3 변화: 

(1) 일차원 메모리 뷰의 슬라이스 기능을 사용할 수 있습니다. 

(2) 'B', 'b', 'c' 포멧의 메모리 뷰를 해시할 수 있습니다

파이썬 버전 3.4 변화:

메모리 뷰는 collections.abc.Sequence 클래스에 자동으로 레지스터(register) 됩니다.

파이썬 버전 3.5 변화:

메모리 뷰는 정수 튜플로 인덱싱이 가능해 집니다 (예: memoryview(b'1234')[1:2])

------

메모리 뷰의 메서드:

## __eq__(exporter)

A memoryview and a PEP 3118 exporter are equal if their shapes are equivalent and if all corresponding values are equal when the operands’ respective format codes are interpreted using struct syntax.

데이터의 차원(모양; shape)이 동일하고 모든 값이 메모리 뷰와 구조체(struct)를 이용한 오퍼렌드(operand)의 포맷을 따르면서 모든 데이터 값들이 동일할 때 메모리 뷰와 PEP 3118에서 정의된 exporter와 같습니다.

For the subset of struct format strings currently supported by tolist(), v and w are equal if v.tolist() == w.tolist():

구조체 포멧 문자열이 tolist() 메서드를 지원한다면, v.tolist() == w.tolist()는 True 입니다:

```python
import array

a = array.array('I', [1,2,3,4,5])
b = array.array('d', [1.0, 2.0, 3.0, 4.0, 5.0])
c = array.array('b', [1,3,5])

am = memoryview(a)
bm = memoryview(b)

f'a  tolist : {a.tolist()}' # [1,2,3,4,5]
f'b  tolist : {b.tolist()}' # [1.0,2.0,3.0,4.0,5.0]
f'am tolist : {am.tolist()}'# [1,2,3,4,5]
f'bm tolist : {bm.tolist()}'# [1.0,2.0,3.0,4.0,5.0]

a == b == am == bm # True

am[::2] == c # True
```

If either format string is not supported by the struct module, then the objects will always compare as unequal (even if the format strings and buffer contents are identical):

비교하려는 메모리 뷰의 대상(메모리 뷰 생성자 인자)이 문자열 구조체 모듈(struct module)을 따르지 않으면, 값과 상관없이 무조건 동일하지 않게 평가할 것입니다.

```python
from ctypes import BigEndianStructure, c_long
class BEPoint(BigEndianStructure):
  _fields_ = [("x", c_long), ("y", c_long)]

point = BEPoint(100, 200)

a = memoryview(point)
b = memoryview(point)

a == point # False

a == b # False
```

Note that, as with floating point numbers, v is w does not imply v == w for memoryview objects.

부동소수점 수의 경우 'v is w' 평가식은 메모리 뷰에 있어 'v == w'와 같은 식을 의미하지 않습니다.

Changed in version 3.3: Previous versions compared the raw memory disregarding the item format and the logical array structure.

파이썬 버전 3.3 변화:

이전 버전에서는 메모리 뷰에 담겨있는 데이터의 포멧 또는 논리적인 배열 구조를 무시하고 데이터 그 자체(비트 수준)를 비교했습니다.

## tobytes(order=None)

Return the data in the buffer as a bytestring. This is equivalent to calling the bytes constructor on the memoryview.

tobytes 메서드는 버퍼에 있는 데이터를 바이트 문자열(bytestring)으로 반환합니다. 이는 메모리 뷰 객체를 바이트 클래스 생성자로 호출하는 것과 동일합니다.

```python

m = memoryview(b"abc")

m.tobytes() # b'abc'
bytes(m) # b'abc'

m.tobytes() == bytes(m) # True
```

For non-contiguous arrays the result is equal to the flattened list representation with all elements converted to bytes.<br> tobytes() supports all format strings, including those that are not in struct module syntax.

연속적이지 않은 배열의 경우 메서드에서 배열을 1차원으로 변환(flattened)한 리스트로 바꾼 뒤 바이트로 변환합니다.<br> tobytes 메서드는 구조체 모듈이 아닌 모든 문자열 포멧을 지원합니다.

New in version 3.8: order can be {‘C’, ‘F’, ‘A’}.<br> When order is ‘C’ or ‘F’, the data of the original array is converted to C or Fortran order.<br> For contiguous views, ‘A’ returns an exact copy of the physical memory.<br> In particular, in-memory Fortran order is preserved.<br> For non-contiguous views, the data is converted to C first.<br> order=None is the same as order=’C’.

파이썬 버전 3.8 변화:

tobytes 메서드 인자 order의 옵션으로 'C', 'F', 'A' 될 수 있습니다.<br> 'C' 또는 'F'로 하는 경우 배열의 원본은 C 언어, 포트란(Fortran)에서 정의한 순서로 정렬됩니다.<br> 연속적으로 존재하는 뷰의 경우, 'A' 옵션은 배열이 저장되어 있는 메모리 주소를 반환합니다.<br> 특히 메모리 내 포트란 순서(옵션 'F')는 유지됩니다.<br> 불연속적으로 존재하는 뷰의 경우 데이터는 C 언어 형식으로 우선 변환됩니다.<br> 옵션을 인자를 비워두는 경우 order='C'와 같은 결과를 반환합니다.

## hex([sep[, bytes_per_sep]])

Return a string object containing two hexadecimal digits for each byte in the buffer.

버퍼에 존재하는 각 바이트(8 비트)마다 16진수로 변환된 문자열을 반환합니다.

```python
m = memoryview(b"abc")
m.hex() # '616263'

hex(ord('a')) # 0x61
hex(ord('b')) # 0x62
hex(ord('c')) # 0x63
```

파이썬 버전 3.5에 새로 생겼습니다.

파이썬 버전 3.8 변화: 

Similar to bytes.hex(), memoryview.hex() now supports optional sep and bytes_per_sep parameters to insert separators between bytes in the hex output.

bytes.hex 메서드와 마찬가지로 memoryview.hex 메서드도 hex 함수의 반환 타입인 바이트(bytes)를 분리 문자(seperator)를 삽입할 수 있는 'sep' 파라미터와 'bytes\_per\_sep' 파라미터를 지원합니다.

## tolist()

버퍼에 있는 데이터를 리스트의 요소로 담은 후 반환합니다.

```python
memoryview(b'abc').tolist() # [97, 98, 99]

import array
a = array.array('d', [1.1, 2.2, 3.3])
m = memoryview(a)
m.tolist() # [1.1, 2.2, 3.3]
```

Changed in version 3.3: tolist() now supports all single character native formats in struct module syntax as well as multi-dimensional representations.

파이썬 버전 3.3 변화:

tolist 메서드는 기존에 다차원 표현에서 지원하는 것과 같이 각각의 문자의 포멧또한 구조체 모듈 문법(stuct module syntax) 지원합니다.
(tolist() now supports all single character native formats in struct module syntax as well as multi-dimensional representations.)

## toreadonly()

기존 메모리 뷰 객체의 데이터와 같은 읽기 전용 메모리 뷰 객체를 반환합니다. 새로 생성한 객체를 반환하므로, 기존의 메모리 뷰 객체는 변하지 않습니다.

```python
m = memoryview(bytearray(b'abc'))
mm = m.toreadonly()
mm.tolist() # [89, 98, 99]

mm[0] = 42
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: cannot modify read-only memory

m[0] = 43
mm.tolist() # [43, 98, 99]
```

파이썬 버전 3.8에 새로 생겼습니다.

## release()

메모리 뷰 객체를 통해서 보고 있는 메모리 버퍼를 해제(release)합니다. 객체에 메모리 뷰를 사용한 경우, 객체의 행동을 제한 할 수 있습니다.<br> 예를 들어 bytearray의 경우 메모리 뷰 객체가 생성되면 크기 조정(resize)을 막습니다. release 함수는 메모리 뷰를 통해 보고 있는 객체를 해제합니다.

release 메서드를 사용하고, release 메서드 외에 다른 호출(call)을 하는 경우 ValueError를 일으킵니다 (해제된 메모리 접근시 Exception을 던지는 것과 유사한 개념)

```python
m = memoryview(b'abc')
m.release() # 메모리 뷰와 메모리 뷰를 통해 보고 있는 객체를 분리합니다
m[0]
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: operation forbidden on released memoryview object
```

with ... as 키워드를 이용한 컨텍스트 매니저 또한 release 함수를 호출한 것과 비슷한 결과를 낼 수 있습니다:

```python
with memoryview(b'abc') as m:
  m[0] # 97

m[0]
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: operation forbidden on released memoryview object
```

파이썬 버전 3.2에 새로 나왔습니다.

## cast(format[, shape])

Cast a memoryview to a new format or shape.<br> shape defaults to [byte\_length//new\_itemsize], which means that the result view will be one-dimensional.<br> The return value is a new memoryview, but the buffer itself is not copied.<br> Supported casts are 1D -> C-contiguous and C-contiguous -> 1D.

메모리 뷰를 새로운 포맷 또는 모양으로 캐스트(cast)합니다.<br>
메모리 뷰의 모양의 기본값은 [byte\_length\/\/new\_itemsize] 입니다.<br>
즉 memoryview.view는 일차원입니다.<br>
반환값은 새로운 메모리 뷰로, 기존에 메모리 뷰를 통해 보고 있는 객체를 얕은 복사(shallow copy)를 통해 새로 만든 것입니다.<br>
1차원에서 N 차원으로 

The destination format is restricted to a single element native format in struct syntax.<br> One of the formats must be a byte format (‘B’, ‘b’ or ‘c’). The byte length of the result must be the same as the original length.

바꾸고자 하는 포멧은 네이티브 구조체 포멧, ‘B’, ‘b’ ,‘c’ 중 하나로 해야합니다.<br>
포멧은 바뀌어도 바이트 길이는 그대로 같습니다.

Cast 1D/long to 1D/unsigned bytes:

1D/long(실수) 에서 1D/unsigned bytes 으로 변환하는 예시:

```python
import array
a = array.array('l', [1,2,3])

x = memoryview(a)
x.format  # 'l'
x.itemsize  # 8
len(x)  # 3
x.nbytes  # 24

y = x.cast('B')
y.format  # 'B'
y.itemsize  # 1
len(y)  # 24
y.nbytes  # 24
```

Cast 1D/unsigned bytes to 1D/char:


```python
b = bytearray(b'zyz')

x = memoryview(b)
x[0] = b'a'
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: memoryview: invalid value for format "B"

y = x.cast('c')
y[0] = b'a'

  # y[0] = b'a'를 했지만, 같은 오브젝트를 두고 메모리 뷰 객체를 생성했기 때문에 (shallow copy) 바뀜
b # bytearray(b'ayz') 

```

Cast 1D/bytes to 3D/ints to 1D/signed char:

```python
import struct

buf = struct.pack("i"*12, *list(range(12)))

x = memoryview(buf)
y = x.cast('i', shape=[2,2,3])
y.tolist() # [[[0, 1, 2], [3, 4, 5]], [[6, 7, 8], [9, 10, 11]]]
y.format # 'i'
y.itemsize # 4
len(y) # 2
y.nbytes # 48

z = y.cast('b')
z.format # 'b'
z.itemsize # 1
len(z) # 48
z.nbytes # 48
```

Cast 1D/unsigned long to 2D/unsigned long:

```python
buf = struct.pack("L"*6, *list(range(6)))

x = memoryview(buf)

y = x.cast('L', shape=[2,3])
len(y) # 2
y.nbytes # 48
y.tolist() # [[0, 1, 2], [3, 4, 5]]
```

파이썬 버전 3.3에 새로 나왔습니다

파이썬 버전 3.5 변화:

바이트 뷰로 캐스팅할 때 포맷에 대한 제한은 없습니다.

Changed in version 3.5: The source format is no longer restricted when casting to a byte view.

## 읽기 전용 (메서드 X) 속성들 :

## obj

The underlying object of the memoryview:

```python
b  = bytearray(b'xyz')
m = memoryview(b)
m.obj is b # True
```

New in version 3.3.

## nbytes

nbytes == product(shape) * itemsize == len(m.tobytes()). This is the amount of space in bytes that the array would use in a contiguous representation. It is not necessarily equal to len(m):

```python
import array
a = array.array('i', [1,2,3,4,5])
m = memoryview(a)
len(m) # 5
m.nbytes # 20
y = m[::2]
len(y) # 3
y.nbytes # 12
len(y.tobytes()) # 12
```

Multi-dimensional arrays:

```python
import struct
buf = struct.pack("d"*12, *[1.5*x for x in range(12)])
x = memoryview(buf)
y = x.cast('d', shape=[3,4])
y.tolist() # [[0.0, 1.5, 3.0, 4.5], [6.0, 7.5, 9.0, 10.5], [12.0, 13.5, 15.0, 16.5]]
len(y) # 3
y.nbytes # 96
```

파이썬 버전 3.3에 새로 나왔습니다.

## readonly

A bool indicating whether the memory is read only.

## format

A string containing the format (in struct module style) for each element in the view. A memoryview can be created from exporters with arbitrary format strings, but some methods (e.g. tolist()) are restricted to native single element formats.

Changed in version 3.3: format 'B' is now handled according to the struct module syntax. This means that memoryview(b'abc')[0] == b'abc'[0] == 97.

## itemsize

The size in bytes of each element of the memoryview:

```python
import array, struct
m = memoryview(array.array('H', [32000, 32001, 32002]))
m.itemsize #2
m[0] # 32000
struct.calcsize('H') == m.itemsize # True
```

## ndim

An integer indicating how many dimensions of a multi-dimensional array the memory represents.

## shape

A tuple of integers the length of ndim giving the shape of the memory as an N-dimensional array.

Changed in version 3.3: An empty tuple instead of None when ndim = 0.

## strides

A tuple of integers the length of ndim giving the size in bytes to access each element for each dimension of the array.

Changed in version 3.3: An empty tuple instead of None when ndim = 0.

## suboffsets

Used internally for PIL-style arrays. The value is informational only.

## c_contiguous

A bool indicating whether the memory is C-contiguous.

New in version 3.3.

## f_contiguous

A bool indicating whether the memory is Fortran contiguous.

New in version 3.3.

## contiguous

A bool indicating whether the memory is contiguous.

New in version 3.3.