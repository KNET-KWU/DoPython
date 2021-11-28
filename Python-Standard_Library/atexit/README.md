# atexit

원본링크 : [python docs - atexit](https://docs.python.org/3/library/atexit.html)

`atexit`는 이름 그대로 파이썬 인터프리터가 종료 될 때 실행할 함수를 레지스터하는 모듈이다.

사용 패턴은 Go 프로그래밍 언어의 defer / panic / recover 패턴과 유사하다.

스크립트 내에 로깅, 파일 포인터, 네트워크 소켓, 스레드, 등

메인 스레드 외에 파일을 열거나, 다른 컴퓨팅 리소스를 사용하는 경우
(스레드의 경우 데몬을 설정하지 않은 경우)

atexit 모듈을 통해서 열린 파일 포인터를 정리하도록 지정해주던가

리소스를 해제할 수 있도록 사용할 수 있다.

하지만, 대부분 컨텍스트(`wuth ... as ...`)를 통해서 관리하는 편이 대부분이며 권장되는 패턴이다.

## atexit.register(func, \*args, \*\*kwargs)

```python
import atexit

def main():
    atexit.register(goodbye)
    print('this is main')
    return

def goodbye():
    print('good bye')

if __name__ == '__main__':
    main()
```

스크립트가 끝났고, 명시적으로 `goodbye` 함수를 호출하지 않아도

마지막에 `goodbye` 함수가 호출 되는 것을 볼 수 있다.

## atexit.unregister(func)

```python
...

def main():
    atexit.register(goodbye)
    print('this is main')
    atexit.unregister(goodbye)
    return

```

반대로 register 된 함수를 `unregister`하여 인터프리터가 종료될 때

호출되지 않도록 설정할 수 있다.
