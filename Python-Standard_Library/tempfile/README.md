# tempfile - 임시파일 만들기

tempfile 모듈은 실제 존재하는 파일처럼 읽기/쓰기가 가능한 임시 파일을 만듭니다.

테스트 용도로 사용되거나, 빠르게 읽기/쓰기가 요구되는 파일의 경우 쓰일 수 있습니다.

## 목차

* 개요

* 반복적으로 사용되는 파라미터

* tempfile를 구성하는 요소들

	***생성자로 쓰이는 것들***

    * TemporaryFile - 임시 파일 만들기
    
    * NamedTemporaryFile - 이름이 있는 임시 파일 만들기
    
    * SpooledTemporaryFile - 일정 크기보다 커지면 일반 파일로 만들기
    
    * TemporaryDirectory - 임시 디렉토리 만들기 

	***함수로 쓰이는 것들***
	   
    * mktemp - 임시 파일 만들기(Deprecated... **하지만..?**)
    
    * mkstemp - 안전한 방식으로 임시 파일 만들기
    
    * mkdtemp - 안전한 방식으로 임시 디렉토리 만들기
    
    ***아래는 약간 다른 것들***
    
    * gettempdir - 임시 파일이 저장된 디렉토리 부르기
    
        * gettempdirb
        
    * gettempprefix - 임시 파일 이름의 접두사 부르기
    
        * gettempprefixb
        
    * tempdir - 임시파일이 저장될 기본값

* 실제로 tempfile이 사용되는 곳 - multiprocess

    * multiprocessing/connection.py

<br>

## 개요

tempfile 모듈은 I/O 작업이 많이 발생하고, 프로세서간에 데이터를 주고 받는 모듈에서 주로 사용됩니다.

임시로 생성되는 파일이고, 또 임시로 생성되어야 하기 때문에 다양한 데이터가 오고갈 수 있습니다.

파이썬으로 개발된 애플리케이션 중에 애플리케이션을 고의로 망치기 위해서 임시파일이 기본적으로 저장하는 위치(`gettempdir`)에

계속 스냅샷을 찍어서 정보를 우회해서 얻는다거나, 빠르게 파일을 대체해서 악용을 하는 등...

간단히 소개한 라이브러리지만, 파이썬 사용자 모두에게 직간접적으로 위협을 가할 수 있는 포텐셜이 있는 라이브러리 같습니다.

## 반복적으로 사용되는 파라미터 

*mode*

*buffering*

*encoding* 

*newline*

*suffix*

*prefix*

*dir*

*errors*

## tempfile를 구성하는 요소들

***생성자로 쓰이는 것들***

### TemporaryFile - 임시 파일 만들기

`TemporaryFile(mode='w+b', buffering=-1, encoding=None, newline=None, suffix=None, prefix=None, dir=None, * , errors=None)`

일반적인 파일 객체와 같이 임시로 사용할 수 있는 파일 객체를 반환합니다.

tempfile의 임시 파일 생성 메서드 `mkstemp()`를 이용해 파일을 생성하기 때문에 안전하게 사용할 수 있습니다.

파일 객체가 GC 되거나 코드에 따라 닫히는 경우 파일은 바로 삭제됩니다.

**UNIX**에서만, 파일의 디렉토리 엔트리가 생성되지 않거나 생성될 경우 바로 지워집니다.<br>

`TemporaryFile` 생성자를 통해 생성된 임시 파일 객체는 컨텍스트 매니저(`with ... as`)에 쓰일 수 있습니다.<br>
컨텍스트 코드 블럭에서 벗어나면 임시파일은 자동으로 삭제됩니다.

```python
from tempfile import TemporaryFile

with TemporaryFile(mode = 'w+', encoding = 'utf-8') as temp:
    temp.write('안녕')
    temp.seek(0)
    print(temp.read())  # 안녕
```

*mode* 파라미터의 기본값은 `w+b`으로 파일을 읽기/쓰기 모두 가능하게 되어 있습니다.<br>
바이너리 모드(`b`)가 기본값으로 되어 있는 이유는 플렛폼과 상관없이 파일을 읽고 쓰기를 지원하기 위해서 입니다.

**POSIX**에서는 실제 파일이 생성되어 반환되지만, 이를 제외한 다른 플렛폼에서는 파일과 유사한 객체를 반환합니다.<br>
**POSIX**를 제외한 다른 플렛폼에서 반환되는 *유사* 파일 객체는 파일 객체의 속성을 모두 가지고 있는 형태를 띄고 있습니다.


(**파이썬 버전 >= 3.8**)

`sys` 모듈의 audit이 됩니다.

함수 이름 : `tempfile.mkstemp`<br>
인자 이름 : (임시 파일의 경로)

```python
# 바로 위의 예시코드에 이어서

import sys

def audit_hook(event, args):
  if event in ['tempfile.mkstemp']:
    print(f'{event=}, {args=}')
  
sys.addaudithook(audit_hook)

# 출력 : event='tempfile.mkstemp', args=('/tmp/[랜덤으로 생성된 이름]',)
```

------

### NamedTemporaryFile - 이름이 있는 임시 파일 만들기

`NamedTemporaryFile(mode='w+b', buffering=-1, encoding=None, newline=None, suffix=None, prefix=None, dir=None, delete=True, *, errors=None)`

이 함수는 파일 시스템에서 볼 수 있는 이름을 가진것 이외에 위에 있는 `TemporaryFile()`과 똑같습니다. (Unix에선 디렉토리 엔트리가 링크되어 있습니다)

객체를 생성할 때 파라미터에 이름을 넣어 지정하는 것이 아니라, 반환된 객체의 `name` 속성에 접근 해서 읽을 수 있습니다.

```python
from tempfile import NamedTemporaryFile

temp = NamedTemporaryFile(mode = 'a+', encoding = 'utf-8')
temp.write('안녕')
temp.seek(0)
print(temp.read())  # 안녕
print(temp.name)    # ../5E035327-61F4-4A89-B340-5343ED33C0C1/tmp/tmpvgulzxjx
```

플렛폼마다 다르지만, 이 이름을 이용해서 임시파일을 **두 번** 이상 사용할 수 있습니다.

```python
# 바로 위의 예시코드에 이어서

with open(temp.name, mode = 'r', encoding = 'utf-8') as fp:
    fp.seek(0)
    print(fp.read())    # 안녕
```

키워드 인자 *delete*가 `True`(기본값)인 경우 `TemporaryFile()`과 같이 파일이 닫힌 후 바로 삭제됩니다.

(**파이썬 버전 >= 3.8**)

`sys` 모듈의 audit이 됩니다.

함수 이름 : `tempfile.mkstemp`<br>
인자 이름 : (임시 파일의 경로)

```python
# 바로 위의 예시코드에 이어서
import sys

def audit_hook(event, args):
  if event in ['tempfile.mkstemp']:
    print(f'{event=}, {args=}')
  
sys.addaudithook(audit_hook)
# 출력 : event='tempfile.mkstemp', args=('/tmp/[랜덤으로 생성된 이름]',)
```

------

### SpooledTemporaryFile - 일정 크기보다 커지면 일반 파일로 만들기
    
우선 메서드에 대해서 알아보기 전에 'Spool'이라는 단어를 먼저 알아보고 가겠습니다.

스풀(SPOOL)은 'Simultaneous Peripheral Operations On Line'의 축약어로 뜻을 풀어쓰면 '연속적인 주변 장치 동작 대기열'입니다.<br>
이해하기 쉽게 말하자면, '작업 대기열을 관리하는 행위'입니다.

CPU를 제외한 다른 처리 장치의 속도가 CPU의 속도에 비해 극도로 느리기 때문에<br>
기존에 상대하고 있는 장치와의 작업을 기다리지 않고, 다른 작업을 하는 것을 의미합니다.

"CPU보다 주변장치의 **속도**가 느리니깐 잠깐 다른거 하고 올게"<br>
라는 말이 `SpooledTemporaryFile`에서는 <br>
"임시메모리의 **크기**가 커서 아예 파일을 하나 만들게"<br>
라고 단어가 쓰였다고 이해하시면 되겠습니다.

...

`SpooledTemporaryFile(max_size=0, mode='w+b', buffering=-1, encoding=-1, newline=None, suffix=None, prefix=None, dir=None, *, errors=None)`

* 키워드 인자로 지정한 최대 크기 *max_size*를 초과할 경우 

* 반환된 임시 파일에 `fileno()` 메서드가 호출되는 경우

실제 파일이 생성되는 점을 제외하면 `TemporaryFile()`와 같습니다.

`rollover()` 메서드를 호출하면 키워드 인자 *max_size*를 초과하지 않아도 파일을 생성합니다.

*_file* 속성은 파일 읽기 방식(binaray, 인코딩)에 따라 `io.BytesIO` 또는 `io.TextIOWrapper` 객체를 반환합니다.<br>
`rollover()` 메서드가 호출된 객체의 경우 실제 파일 객체가 반환됩니다.

`rollover()`가 호출 된 이상 `open()`으로 생성한 일반 파일과 같습니다.

```python
from tempfile import SpooledTemporaryFile

Stemp_binary = SpooledTemporaryFile(mode = 'w+b')
Stemp_utf8 = SpooledTemporaryFile(mode = 'w+', encoding = 'utf-8')

print(type(Stemp_binary._file))    # <class '_io.BytesIO'>
print(type(Stemp_utf8._file))        # <class '_io.StringIO'>

# 실제 파일
with open('mytempfile', mode ='w+') as f:
    print(type(f.buffer))    # <class '_io.BufferedRandom'>

with SpooledTemporaryFile() as f:
    f.fileno()
    print(type(f._file))    # <class '_io.BufferedRandom'>
    
with SpooledTemporaryFile() as f:
    f.rollover()
    print(type(f._file))    # <class '_io.BufferedRandom'>
```


(**버전 >= 3.8**)

`sys` 모듈의 audit이 됩니다.

함수 이름 : `tempfile.mkstemp`<br>
인자 이름 : (임시 파일의 경로)

```python
# 바로 위의 예시코드에 이어서
import sys

def audit_hook(event, args):
  if event in ['tempfile.mkstemp']:
    print(f'{event=}, {args=}')
  
sys.addaudithook(audit_hook)
# 출력 : event='tempfile.mkstemp', args=('/tmp/[랜덤으로 생성된 이름]',)
```

-------

### TemporaryDirectory - 임시 디렉토리 만들기 

`TemporaryDirectory(suffix=None, prefix=None, dir=None)`

`mkdtemp()`와 같은 방식으로 임시 디렉토리를 생성합니다.<br>
`TemporaryFile`과 같이 컨텍스트 매니저에 사용될 수 있습니다.

```python
with TemporaryDirectory() as td:
    pass
```

임시로 생성된 디렉토리에 생성된 파일들은 임시 디렉토리 객체와 함께 삭제됩니다.

`NamedTemporaryFile`와 같이 *name* 속성을 통해 임시로 생성된 디렉토리의 이름을 읽을 수 있습니다.

(**파이썬 버전 >= 3.8**)

`sys` 모듈의 audit이 됩니다.

함수 이름 : `tempfile.mkdtemp`<br>
인자 이름 : (임시 파일의 경로)
```python
# 바로 위의 예시코드에 이어서
import sys

def audit_hook(event, args):
  if event in ['tempfile.mkdtemp']:
    print(f'{event=}, {args=}')
  
sys.addaudithook(audit_hook)
# 출력 : event='tempfile.mkdtemp', args=('/tmp/[랜덤으로 생성된 이름]',)
```

<br>

***함수로 쓰이는 것들***

### mktemp - 임시 파일 만들기(Deprecated... **하지만..?**)

### mkstemp - 안전한 방식으로 임시 파일 만들기

### mkdtemp - 안전한 방식으로 임시 디렉토리 만들기

<br>

***약간 다른 것들***

### gettempdir - 임시 파일이 저장된 디렉토리 부르기

#### gettempdirb
    
### gettempprefix - 임시 파일 이름의 접두사 부르기

#### gettempprefixb
    
### tempdir - 임시파일이 저장될 기본값

<br>



## 실제로 tempfile이 사용되는 곳 - multiprocess

모든 데이터의 형태는 프로세서에서 프로세서로 전달되기 이전에 무조건 파일 자체로 존재하므로 multiprocess 모듈을 소개합니다.

멀티 쓰레딩처럼 데이터가 쉽게 공유가능 것과 달리, 멀티 프로세싱은 연산을 하는 프로세서가 다르기 때문에<br>
데이터를 공유하기 위해서는 메모리 상에서 임시로 접근 가능한(권한이 주어진) 프로세서들을 한정으로 공유할 수 있도록 해줍니다.

### multiprocessing/connection.py

프로세스 간에 데이터를 주고 받기위해서 사용되는 `Connection` 객체 중 잘알려진 `Pipe` 객체의 경우 
 [multiprocessing/connection.py](https://github.com/python/cpython/blob/eba45a8ea704a7d898e5721ee2811344969c0d9e/Lib/multiprocessing/connection.py#L69)를 보면 

```python
def arbitrary_address(family):
    ...
    elif family == 'AF_PIPE':
        return tempfile.mktemp(prefix=r'\\.\pipe\pyc-%d-%d-' %
                               (os.getpid(), next(_mmap_counter)), dir="")
    ...
```

위 코드와 같이 메모리에 접근 가능하도록 임시파일을 생성합니다.

`tempfile.mktemp` 메서드를 통해서 생성된 파일은 프로그램이 종료된 후 파일을 삭제를 보장하므로 의도한 대로 작동할 것입니다.

근데 `tempfile.mktemp`는 파이썬 버전 2.3부터 deprecated된 메서드인데 3.9 버전까지 아직도 쓰이는 걸 보면... 음... 바쁘니깐 그럴수도 있을 것 같습니다.