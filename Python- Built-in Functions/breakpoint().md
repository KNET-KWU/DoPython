# breakpoint()

에디터에 있던 빨간점 디버거를 코드로 사용하자

## 목차

* 파이썬 공식 문서 - 한글(번역)
    
* 파이썬 공식 문서 - 영어(원문)

## 파이썬 공식 문서 - 한글(번역)

`breakpoint(*args, **kws)`

이 함수가 호출되면 디버깅 모드로 진입합니다.

`breakpoint()`는 [sys.breakpointhook()](https://docs.python.org/3/library/sys.html#sys.breakpointhook) 함수를 *args*과 *kws* 인자를 전달한 것과 같습니다.

호출된 `sys.breakpointhook()` 함수는 이어서 [pdb.set_trace()](https://docs.python.org/3/library/pdb.html#pdb.set_trace) 함수를 호출합니다.

명시적으로 `import pdb`라고 작성할 필요없이 `pdb.set_trace` 함수가 호출됩니다.

`sys.breakpointhook` 함수에 다른 값 대입해서 디버깅을 할지 안할지 선택할 수 있습니다.<br>
`breakpoint`는 단지 인자와 함께 `sys.breakpointhook`을 호출한 것과 같기 때문입니다.

```python
import sys

# in product release
release = True

if release:
    # breakpoint를 호출해도 pdb(파이썬 디버깅) 모드가 시행되지 않고 계속 실행됩니다
    sys.breakpointhook = lambda : None

name = 'ccppoo'

breakpoint() # repl mode on 디버깅 시작

print('Hello!')
```

`breakpoint` 함수가 호출되면 [auditing event](https://docs.python.org/3/library/sys.html#auditing) `Exception`을 던집니다.

파이썬 버전 3.7에 생긴 함수입니다

<br>

## 파이썬 공식 문서 - 영어(원문)

`breakpoint(*args, **kws)`

This function drops you into the debugger at the call site. Specifically, it calls [sys.breakpointhook()](https://docs.python.org/3/library/sys.html#sys.breakpointhook), passing `args` and `kws` straight through.<br>

By default, `sys.breakpointhook()` calls [pdb.set_trace()](https://docs.python.org/3/library/pdb.html#pdb.set_trace) expecting no arguments.<br>

In this case, it is purely a convenience function so you don’t have to explicitly import [pdb](https://docs.python.org/3/library/pdb.html#module-pdb) or type as much code to enter the debugger.<br>

However, [sys.breakpointhook()](https://docs.python.org/3/library/sys.html#sys.breakpointhook) can be set to some other function and (breakpoint()](https://docs.python.org/3/library/functions.html#breakpoint) will automatically call that, allowing you to drop into the debugger of choice.

Raises an [auditing event](https://docs.python.org/3/library/sys.html#auditing) `builtins.breakpoint` with argument `breakpointhook`.

New in version 3.7.