# partialmethod

일반 함수에 `partial`를 사용하는 것처럼, 메서드에 `partialmethod`를 사용하는 것과 같습니다.

`functools.partialmethod(func, /, *args, **keywords)`

새로운 `partialmethod` 디스크립터(descriptor)를 반환합니다.

일반 메서드에 미리 인자를 대입한 형태인 [partial()](./partial.md)과 똑같이 작동하지만,<br>
직접 호출 가능한(callable) 함수가 아닌, 메서드 명세(method definition)과 같이 작동합니다.

<br>

첫번째 인자 `func`는 호출 가능한 객체(callabe) 또는 디스크립터이어야 합니다.<br>
디스크립터인 것과 동시에 호출 가능한 객체인 **함수**는 디스크립터로 취급됩니다.

> '디스크립터'임과 동시에 호출가능한 객체는 **함수**입니다

1. 첫번째 인자 *func*에 디스크립터(일반 함수, 클래스 메서드, 정적 메서드, 추상 메서드)가 주어진 경우,<br>
`__get__`을 통한 호출을 하는 경우 *func* 인자인 객체와 협력하고 있는 디스크립터와 협력하는 `partialmethod` 객체를 반환합니다.

2. 첫번째 인자 *func*에 디스크립터가 아닌 호출가능한 객체일 경우 클래스 인스턴스에 의존하는 메서드를 동적으로 생성합니다(bound method).<br>
인자 *args*, *keywords*에 인자를 정의해도 일반적인 메서드와 같이 첫번째 인자에 `self`가 삽입됩니다.

> 디스크립터가 아님과 동시에 호출 가능한 객체는 `__call__`이 정의된 객체입니다.

예시:

```python
class Cell:
    def __init__(self):
        self._alive = False

    @property
    def alive(self):
        return self._alive
    
    def set_state(self, state):
        self._alive = bool(state)
    
    set_alive = partialmethod(set_state, True)
    set_dead = partialmethod(set_state, False)

c = Cell()
c.alive # False
c.set_alive()
c.alive # True
```
