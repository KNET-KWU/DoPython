# tempfile - 임시파일 만들기

tempfile 모듈은 실제 존재하는 파일처럼 읽기/쓰기가 가능한 임시 파일을 만듭니다.

테스트 용도로 사용되거나, 빠르게 읽기/쓰기가 요구되는 파일의 경우 쓰일 수 있습니다.

## 목차

* 개요

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

## tempfile를 구성하는 요소들

***생성자로 쓰이는 것들***

### TemporaryFile - 임시 파일 만들기
    
### NamedTemporaryFile - 이름이 있는 임시 파일 만들기
    
### SpooledTemporaryFile - 일정 크기보다 커지면 일반 파일로 만들기
    
우선 메서드에 대해서 알아보기 전에 'Spool'이라는 단어를 먼저 알아보고 가겠습니다.

스풀(SPOOL)은 'Simultaneous Peripheral Operations On Line'의 축약어로 뜻을 풀어쓰면 '연속적인 주변 장치 동작 대기열'입니다.<br>
이해하기 쉽게 말하자면, '작업 대기열을 관리하는 행위'입니다.

언어 자체는 CPU를 제외한 다른 처리 장치의 속도가 CPU의 속도에 비해 극도로 느려서 처리하고 있는 작업을 대기 상태로 놓고, 다른 작업을 하는 것을 의미합니다.

주변장치의 **속도**가 느려서 잠깐 다른거 하고 올게라는 의미가 `SpooledTemporaryFile`에서는 임시메모리의 **크기**가 커서 아예 파일을 하나 만들게 이해하시면 됩니다.

### TemporaryDirectory - 임시 디렉토리 만들기 

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