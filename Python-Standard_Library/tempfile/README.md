# tempfile - 임시파일 만들기

tempfile 모듈은 실제 존재하는 파일처럼 읽기/쓰기가 가능한 임시 파일을 만듭니다.

테스트 용도로 사용되거나, 빠르게 읽기/쓰기가 요구되는 파일의 경우 쓰일 수 있습니다.

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