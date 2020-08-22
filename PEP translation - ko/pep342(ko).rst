PEP: 342
Title: Coroutines via Enhanced Generators
Version: $Revision$
Last-Modified: $Date$
Author: Guido van Rossum, Phillip J. Eby
Status: Final
Type: Standards Track
Content-Type: text/x-rst
Created: 10-May-2005
Python-Version: 2.5
Post-History:

참고!

다음에 작성된 내용의 예시는 현재 파이썬(3.5 ~)에 일치하지 않는 부분이 있으며,

도입된 취지와 개념을 이해하는 것을 목적으로 읽어주시길 바랍니다!

Introduction
============

This PEP proposes some enhancements to the API and syntax of generators, to
make them usable as simple coroutines.  It is basically a combination of ideas
from these two PEPs, which may be considered redundant if this PEP is
accepted:

이 PEP은 제너레이터(generator)의 API와 문법(syntax)을 간단한 코루틴과 같이 사용 가능하도록 제안합니다. 
기본적으로 다음 두 가지의 PEP의 개념을 조합한 것입니다, 그래서 이 PEP이 승낙된다면 중복이 될 수 있겠네요:

- PEP 288, Generators Attributes and Exceptions.  The current PEP covers its
  second half, generator exceptions (in fact the ``throw()`` method name was
  taken from PEP 288).  PEP 342 replaces generator attributes, however, with a
  concept from an earlier revision of PEP 288, the *yield expression*.

-  PEP 288,  제너레이터의 속성(Attributes)과 예외(Exceptions).
   PEP 288의 절반의 뒷 부분(제너레이터의 예외)을 다루고 있습니다, 사실 여기서 ``throw()`` 매서드 이름을 여기서 따왔습니다.
   현재 작성하는 PEP에서는 제너레이터 속성을 대체하는 것을 제안합니다, 초기 PEP 288 개정판의 컨셉 *yield expression*과 함께요.

- PEP 325, Resource-Release Support for Generators.  PEP 342 ties up a few
  loose ends in the PEP 325 spec, to make it suitable for actual
  implementation.

-  PEP 325, 제너레이터를 위한 리소스-해제(Resource-Release) 지원,
   현재 작성하는 PEP에서는 PEP 325에서 모호하게 다룬 내용을 명확하게 정리하여, 실제로 구현을 하기 위한 기반을 마련합니다. 

Motivation
==========

동기
====

Coroutines are a natural way of expressing many algorithms, such as
simulations, games, asynchronous I/O, and other forms of event-driven
programming or co-operative multitasking.  Python's generator functions are
almost coroutines -- but not quite -- in that they allow pausing execution to
produce a value, but do not provide for values or exceptions to be passed in
when execution resumes.  They also do not allow execution to be paused within
the ``try`` portion of ``try/finally`` blocks, and therefore make it difficult
for an aborted coroutine to clean up after itself.

코루틴(Coroutines)은 많은 알고리즘을 표현할 수 있는 자연스러운 방법입니다.
예를 들어 시뮬레이션, 게임, 비동기 입출력, 그리고 이벤트-주도(event-driven) 프로그래밍 또는 협동적 멀티테스킹이 있습니다.
값(value)을 산출하기 위해서 실행을 잠시 중단하는게 가능한다는 점에서 -- 정확히는 아니지만 -- 파이썬의 제너레이터 함수는 거의 코루틴입니다.
다만, 중단된 실행을 다시 되돌릴 때 값이나 예외(exception)를 전달하지 못합니다.
현재의 제네레이터 함수는 ``try/finally`` 블럭의 ``try`` 부분에서 실행을 중단시킬 수 없어서, 문제가 생긴 코루딘이 스스로 정리할 수 있는 방법을 구현할 수도 없었습니다.

Also, generators cannot yield control while other functions are executing,
unless those functions are themselves expressed as generators, and the outer
generator is written to yield in response to values yielded by the inner
generator.  This complicates the implementation of even relatively simple use
cases like asynchronous communications, because calling any functions either
requires the generator to *block* (i.e. be unable to yield control), or else a
lot of boilerplate looping code must be added around every needed function
call.

그리고, 제너레이터는 다른 함수가 실행중일 때 제어권(control)를 양도(yield)할 수 없습니다, 그 다른 함수가 제너레이터가 아니거나
내부의 제너레이터(inner generator)에 의해 양도된 값이 외부의 제너레이터(outer generator)에 의해 응답(response - callback?)양도되도록 작성되지 않는 경우에 말이죠.
이러한 특징 때문에 간단한 비동기 통신 조차 구현하는데 문제를 일으킵니다.
왜냐하면 다른 함수를 호출하려면 제네레이터를 *블럭(block)* 한 뒤에 계속 실행하거나(제어권을 양도할 수 없어서), 엄청나게 많은 루프로 이루어진 코드를 작성해야하기 때문입니다(콜백지옥).

# !!!!!! 위에서 말한 inner gennerator , outer generator 예시 찾아보기 !!!!!!!

However, if it were possible to pass values or exceptions *into* a generator at
the point where it was suspended, a simple co-routine scheduler or *trampoline
function* would let coroutines *call* each other without blocking -- a
tremendous boon for asynchronous applications.  Such applications could then
write co-routines to do non-blocking socket I/O by yielding control to an I/O
scheduler until data has been sent or becomes available.  Meanwhile, code that
performs the I/O would simply do something like this::

그러나, 값이나 예외를 제너레이터가 중단된 지점으로 전달할 수 있다면, 간단한 코루틴 스케쥴러(scheduler) 또는 트램펄린(trampoline) 함수를 블락 없이 코루틴을 서로 호출할 수 있었을 겁니다 -- 비동기 애플리케이션을 위한 엄청난 혜택이겠죠. 
이러한 애플리케이션은 그러면 I/O 스케줄러에 제어권을 양도하면서 데이터가 모두 송신되거나 유효할 때 논블럭킹(non-blocking) 소켓 I/O를 사용할 수 있는 코루틴을 작성할 수 있겠죠.
이런게 구현되기 전에 I/O를 구현했던 코드는 다음과 같이 비슷하게 했겠죠 :: 


    data = (yield nonblocking_read(my_socket, nbytes))
    
    data = (yield nonblocking_read(my_socket, nbytes))

in order to pause execution until the ``nonblocking_read()`` coroutine produced
a value.

실행을 ``nonblocking_read()`` 까지 중단시키기 위해서는 코루틴은 값을 산출했습니다.

In other words, with a few relatively minor enhancements to the language and to
the implementation of the generator-iterator type, Python will be able to
support performing asynchronous operations without needing to write the entire
application as a series of callbacks, and without requiring the use of
resource-intensive threads for programs that need hundreds or even thousands of
co-operatively multitasking pseudothreads.  Thus, these enhancements will give
standard Python many of the benefits of the Stackless Python fork, without
requiring any significant modification to the CPython core or its APIs.  In
addition, these enhancements should be readily implementable by any Python
implementation (such as Jython) that already supports generators.

다르게 표현하자면, 몇가지 사소한 발전과 제너레이터-이터레이터(generator-iterator) 타입의 구현만 이루어진다면, 파이썬은 콜백 지옥과 수백 수천개의 동작하는 멀티태스킹 슈도스레드(co-operatively multitasking pseudothreads)를 이용하는 끔찍한 스레드 구조 없이 비동기 작업을 지원할 수 있을 겁니다.
따라서, 이러한 발전은 CPython의 코어 또는 API의 큰 수정 없이도, 파이썬의 파생형(Stackless Python fork)의 장점들을 표준 파이썬에서도 쓸 수 있게 만들어 줄 겁니다.
추가로, 이러한 발전은 이미 제너레이터를 지원하는 모든 파이썬 확장형(예 : Java + Python : Jython)에 또한 즉시 구현될 수 있을 겁니다.

### !!!!! Stackless Python fork 이 뭐지? !!!!!!

Specification Summary
=====================

By adding a few simple methods to the generator-iterator type, and with two
minor syntax adjustments, Python developers will be able to use generator
functions to implement co-routines and other forms of co-operative
multitasking.  These methods and adjustments are:

제너레이터-이터레이터 타입에 몇가지 간단한 메서드를 추가하고, 두 가지의 간단한 문법 수정을 통해서, 파이썬 개발자들은 제너레이터 함수를 코루틴과 협업 멀티테스킹에 사용할 수 있을 겁니다(co-operative multitasking).
이러한 메서드와 변경사항은 :

1. Redefine ``yield`` to be an expression, rather than a statement. The current
   yield statement would become a yield expression whose value is thrown away.
   A yield expression's value is ``None`` whenever the generator is resumed by
   a normal ``next()`` call.

1. ``yield``를 문(statement)에서 표현식(expression)으로 재정의합니다.
   현재의 yield 문은 값을 산출하는 yield 표현식으로 바뀝니다.
   yield 표현식의 값은 일반적인 ``next()``호출로 인해 재시작(resume) 되었을 경우 None 입니다.

2. Add a new ``send()`` method for generator-iterators, which resumes the
   generator and *sends* a value that becomes the result of the current
   yield-expression.  The ``send()`` method returns the next value yielded by
   the generator, or raises ``StopIteration`` if the generator exits without
   yielding another value.

2. 제너레이터-이터레이터에 새로운 ``send()`` 메서드를 추가합니다.
   이 메서드는 제너레이터에 yield 표현식의 결과가 되는 값을 *전달(send)*합니다.
   ``send()``메서드는 제너레이터에 의해 yield 된 값을 반환하거나, 제너레이터가 다른 값을 yield하지 않은 채 exit되는 경우 ``StopIteration``을 raise 합니다.

3. Add a new ``throw()`` method for generator-iterators, which raises an
   exception at the point where the generator was paused, and which returns the
   next value yielded by the generator, raising ``StopIteration`` if the
   generator exits without yielding another value.  (If the generator does not
   catch the passed-in exception, or raises a different exception, then that
   exception propagates to the caller.)

3. 제네레이터-이터레이터에 ``throw()``메서드를 추가합니다.
   이 메서드는 제너레이터가 멈춘 곳에서 예외(exception)을 raise하며, 제너레이터에 의해 yield된 값을 반환합니다.
   제너레이터가 yield할 값이 없이 exit하는 경우 ``StopIteration``을 raise 합니다.
   (만약 제너레이터가 전달된(passed-in) 예외나 또 다른 예외를 잡지(catch) 못한 경우, 그 예외를 caller에게 전달합니다)

4. Add a ``close()`` method for generator-iterators, which raises
   ``GeneratorExit`` at the point where the generator was paused.  If the
   generator then raises ``StopIteration`` (by exiting normally, or due to
   already being closed) or ``GeneratorExit`` (by not catching the exception),
   ``close()`` returns to its caller.  If the generator yields a value, a
   ``RuntimeError`` is raised.  If the generator raises any other exception, it
   is propagated to the caller. ``close()`` does nothing if the generator has
   already exited due to an exception or normal exit.

4. 제네레이터-이터레이터에 ``close()``메서드를 추가합니다.
   이 메서드는 제너레이터가 멈춘 곳에서 ``GeneratorExit``를 raise합니다.
   만약 제너레이터가 ``StopIteration``(정상적으로 exit 되거나, 닫힌 경우) 그리고 ``GeneratorExit``(예외를 catch 하지 않는 경우)를 raise한 경우, ``close()`` 메서드가 caller로 돌아갑니다(제어권 넘김)
   만약 제너레이터가 값을 yield한 경우에는 ``RuntimeError``를 raise 합닏ㅏ.
   만약 제너레이터가 위에 언급된 예외를 제외한 다른 예외를 raise하는 경우, caller에게 전달합니다.
   만약 제너레이터 내부의 예외 또는 정상적인 exit를 하는 경우 ``close()``는 아무것도 하지 않습니다.

5. Add support to ensure that ``close()`` is called when a generator iterator
   is garbage-collected.

5. 추후에 제너레이터-이터레이터가 GC(garbage-collected)될 때 ``close()``가 호출되도록 지원합니다.

6. Allow ``yield`` to be used in ``try/finally`` blocks, since garbage
   collection or an explicit ``close()`` call would now allow the ``finally``
   clause to execute.

6. ``yield`` 표현식이 ``try/finally``블럭(block)에서 쓰일 수 있도록 합니다.
   GC(가 되는 순간) 또는 명확한 ``close()`` 호출이 ``finally`` 구간에서 실행될 수 있기 때문입니다.

A prototype patch implementing all of these changes against the current Python
CVS HEAD is available as SourceForge patch #1223381
(https://bugs.python.org/issue1223381).

위의 모든 것을 구현하도록 한 프로토타입 패치를 진행한 현재의 Python CVS HEAD는 SourceForge patch #1223381에서 확인 가능합니다.
(https://bugs.python.org/issue1223381).

Specification: Sending Values into Generators
=============================================

설명서 : 제너레이터에 값 전달하기
==========================

New generator method: ``send(value)``
-------------------------------------

새로운 제너레이터 메서드 : ``send(value)``------------------------------------

A new method for generator-iterators is proposed, called ``send()``.  It
takes exactly one argument, which is the value that should be *sent in* to
the generator.  Calling ``send(None)`` is exactly equivalent to calling a
generator's ``next()`` method.  Calling ``send()`` with any other value is
the same, except that the value produced by the generator's current
yield expression will be different.

제너레이터-이터레이터에 ``send()``메서드를 새롭게 제안합니다.
제너레이터에 *전달*될 하나의 값(argument)만 받습니다.
``send(None)``을 호출하는 것은 ``next()`` 메서드를 호출하는 것과 같습니다.
(역자 주 : 둘 다 제너레이터를 실행하는 것인데, 값을 전달하느냐 마느냐의 차이다)
``send()``메서드와 함께 어떤 값이든 호출하는 것은 똑같으나, 제너레이터의 yield 표현식을 통해 산출된 값과는 다릅니다.

Because generator-iterators begin execution at the top of the generator's
function body, there is no yield expression to receive a value when the
generator has just been created.  Therefore, calling ``send()`` with a
non-``None`` argument is prohibited when the generator iterator has just
started, and a ``TypeError`` is raised if this occurs (presumably due to a
logic error of some kind).  Thus, before you can communicate with a
coroutine you must first call ``next()`` or ``send(None)`` to advance its
execution to the first yield expression.

왜냐하면 방금 막 생성된 제너레이터-이터레이터는 값을 받을 수 있는 yield 표현식이 존재하지않고, 제너레이터 코드 맨위에서 실행(execution)이 시작되기 때문입니다.
그래서 ``send()``를 non-None 값과 함께 호출하는 것(예 : send(123))은 제너레이터가 방금 막 시작된 경우에 지양됩니다.
만약 non-None 값과 호출되는 경우 ``TypeError``를 raise합니다 (아마 논리적 이유로 인해서요).
그래서 코루틴과 상호작용 하기 전에, 첫번째 yield 표현식을 실행하기 전에 당신은 반드시 ``next()`` 또는 ``send(None)``을 호출해야 합니다.
(역자 주 : yield 이전까지 있는 코드까지 도달하고 yield를 만나면, 제어권을 다시 코루틴 밖(코루틴을 호출한 구문)으로 전달할 수 있기 때문 - 제어권을 서로 주고받는게 곧 코루틴의 역할이니깐...)

As with the ``next()`` method, the ``send()`` method returns the next value
yielded by the generator-iterator, or raises ``StopIteration`` if the
generator exits normally, or has already exited.  If the generator raises an
uncaught exception, it is propagated to ``send()``'s caller.

``next()`` 메스도 마찬가지로, ``send()``메서드는 제너레이터-이터레이터에서 yield된 값을 반환하거나, 제너레이터가 정상적으로 종료되거나, 이미 종료된 경우 ``StopIteration``을 raise합니다.
만약 제너레이터가 처리되지 않은(uncaught) 예외를 raise하는 경우, ``send()``의 caller에게 전달됩니다.

New syntax: Yield Expressions
-----------------------------

새로운 문법 : Yield 표현식
-----------------------------

The yield-statement will be allowed to be used on the right-hand side of an
assignment; in that case it is referred to as yield-expression.  The value
of this yield-expression is ``None`` unless ``send()`` was called with a
non-``None`` argument; see below.

yield문 (yield-statement)은 구문의 오른쪽에 쓰일 겁니다 ; 이 경우 yield 표현식으로 간주될 것입니다.
이 yield 표현식의 값은 ``send()`` 메서드를 통해서 non-None 값이 전달되지 않는 이상 기본적으로 ``None``일 것입니다.
다음을 보세요.

A yield-expression must always be parenthesized except when it occurs at the
top-level expression on the right-hand side of an assignment.  So

yield 표현식은 할당의 오른쪽에있는 최상위 표현식이 아닌 모든 경우, 항상 괄호로 묶어야합니다.

::

    x = yield 42
    x = yield
    x = 12 + (yield 42)
    x = 12 + (yield)
    foo(yield 42)
    foo(yield)

are all legal, but

위는 옳바른 방식이지만, 

::

    x = 12 + yield 42 // 괄호 빠짐
    x = 12 + yield // 괄호 빠짐
    foo(yield 42, 12) // 괄호 빠짐 
    foo(yield, 12) // 괄호 빠짐

are all illegal.  (Some of the edge cases are motivated by the current
legality of ``yield 12, 42``.)

이건 옳바르지 않습니다. (``yield 12, 42`` 같이 특수한 경우의 파생형입니다)

Note that a yield-statement or yield-expression without an expression is now
legal.  This makes sense: when the information flow in the ``next()`` call
is reversed, it should be possible to yield without passing an explicit
value (``yield`` is of course equivalent to ``yield None``).

(평가되고 값을 반환할 수 있는)표현식이 없는 yield문 또는 yield 표현식이 이제 금지된 것을 주목하세요.
이건 말이 됩니다: ``next()``를 통해 값이 전달되는 경우 호출(call)은 예정되어 있는 상태이고, 특정한 값을 전달하지 않고도 yield를 할 수 있어야 합니다 ( ``yield``는 ``yield None``과 동일한 표현입니다).

When ``send(value)`` is called, the yield-expression that it resumes will
return the passed-in value.  When ``next()`` is called, the resumed
yield-expression will return ``None``.  If the yield-expression is a
yield-statement, this returned value is ignored, similar to ignoring the
value returned by a function call used as a statement.

``send(value)``가 호출되는 경우, 제너레이터를 다시 시작하는 yield 표현식은 전달된(passed-in) 값을 반환할 것입니다.
``next()``가 호출되는 경우, 다시 시작된 yield 표현식은 ``None``을 반환할 것입니다.
만약 yield 표현식이 yield 문인 경우, 반환된 값은 무시됩니다.
마치 문(statement) 처럼 사용된 함수의 반환 값을 무시하듯이 말이죠
(역주 : returnSomething()를 호출하고 반환값을 어떤 변수에 할당하지 않는 경우 - discardableResult)

In effect, a yield-expression is like an inverted function call; the
argument to yield is in fact returned (yielded) from the currently executing
function, and the *return value* of yield is the argument passed in via
``send()``.

사실상, yield 표현식은 뒤집힌 함수 호출과 같습니다.
yield 할 파라미터(argument)가 사실은 현재 실행중인 함수로부터 반환(returned) (yield) 된 것이며,
yield의 *반환된 값*은 ``send()``를 통해 전달된 값(argument)입니다.

Note: the syntactic extensions to yield make its use very similar to that in
Ruby.  This is intentional.  Do note that in Python the block passes a value
to the generator using ``send(EXPR)`` rather than ``return EXPR``, and the
underlying mechanism whereby control is passed between the generator and the
block is completely different.  Blocks in Python are not compiled into
thunks; rather, ``yield`` suspends execution of the generator's frame.  Some
edge cases work differently; in Python, you cannot save the block for later
use, and you cannot test whether there is a block or not. (XXX - this stuff
about blocks seems out of place now, perhaps Guido can edit to clarify.)

참고 : yield 문법의 확장은 루비(Ruby)와 유사하게 끔 만들어 줍니다. 의도적으로요.
파이썬은 블럭(block)이 제너레이터에 ``return EXPR`` 보다 ``send(EXPR)``를 이용해 값을 전달하는 것이
이는 제너레이터와 블럭이 전달(전이)하는 제어권의 메커니즘과 아예 다르다는 점을 인지하길 바랍니다.
파이썬에서 블럭(Block)은 thunk로 컴파일 되지않는 않습니다, 반면, ``yield``는 제너레이터 프레임의 실행을 중단시킵니다.
특수한 경우에는 다르게 작동할 수 있습니다; 파이썬에서는 나중에 사용하기 위해서 블럭(스크립트 일부)을 임시 저장해 둘 수 없습니다.
그리고 사용하고자하는 블럭 자체가 있는지 없는지 확인할 수도 없죠.
(위 내용은 옛날 파이썬 이야기인 것 같네요, 귀도 반 로썸이 확실하게 말해줄 수 있겠네요)

Specification: Exceptions and Cleanup
=====================================

설명서 : 예외와 정리(cleanup)
=====================================

Let a generator object be the iterator produced by calling a generator
function.  Below, *g* always refers to a generator object.

제너레이터 오브젝트가 제너레이터 함수를 통해 생성된 이터레이터라고 합시다.
아래의 *g*는 제너레이터 오브젝트를 의미합니다.

New syntax: ``yield`` allowed inside ``try-finally``
----------------------------------------------------

새 문법 : try-finally 블럭 내부에 yield 허용하기
----------------------------------------------------

The syntax for generator functions is extended to allow a yield-statement
inside a ``try-finally`` statement.

제너레이터 함수 내부에 있는 try-finally 블럭 내부에 yeild 문이 허용될 수 있도록 합니다.

New generator method: ``throw(type, value=None, traceback=None)``
-----------------------------------------------------------------

새로운 제너레이터 메서드: ``throw(type, value=None, traceback=None)``
-----------------------------------------------------------------


``g.throw(type, value, traceback)`` causes the specified exception to be
thrown at the point where the generator *g* is currently suspended (i.e. at
a yield-statement, or at the start of its function body if ``next()`` has
not been called yet).  If the generator catches the exception and yields
another value, that is the return value of ``g.throw()``.  If it doesn't
catch the exception, the ``throw()`` appears to raise the same exception
passed it (it *falls through*).  If the generator raises another exception
(this includes the ``StopIteration`` produced when it returns) that
exception is raised by the ``throw()`` call.  In summary, ``throw()``
behaves like ``next()`` or ``send()``, except it raises an exception at the
suspension point.  If the generator is already in the closed state,
``throw()`` just raises the exception it was passed without executing any of
the generator's code.


``g.throw(type, value, traceback)``은 제너레이터 *g*가 중단된 지점에서 즉시 특정한 예외를 던질 수 있습니다.
예를 들면 yield-문 또는 ``next()``를 호출하지 않았을 경우 함수 자체에서 예외를 던질 수 있습니다.
만약 제너레이터가 예외를 catch 하고 다른 값을 yield하는 경우, 그 값 자체가 ``g.throw()``의 값입니다.
만약 예외를 catch하지 못한 경우, 같은 예외를 ``throw()``를 통해 던져집니다 (이 예외는 계속해서 전달됩니다).
만약 제너레이터가 다른 예외를 raise 하는 경우(``StopIteration`` 포함) 이 또한 ``throw()`` 호출을 통해 raise됩니다.
요약을 하면, ``throw()``메서드는 ``next()`` 또는 ``send()`` 메서드와 같이 작동하지만 (예외를 호출자에게 전달하는 것과 같이),
중단한 지점에서 riase한 다는 것과 다릅니다.
만약 제너레이터가 닫힌 상태(closed state)인 경우, ``throw()``는 제너레이터 코드를 실행하지 않은채로 예외를 raise 합니다.

The effect of raising the exception is exactly as if the statement::

    raise type, value, traceback
    
예외를 raise 하는 효과는 다음 구문이  ::

    raise type, value, traceback
    
was executed at the suspension point.  The type argument must not be
``None``, and the type and value must be compatible.  If the value is not an
instance of the type, a new exception instance is created using the value,
following the same rules that the ``raise`` statement uses to create an
exception instance.  The traceback, if supplied, must be a valid Python
traceback object, or a ``TypeError`` occurs.

중단 지점에서 실행되는 것과 똑같이 행동합니다.
이 때 타입 인자(argument)는 반드시 ``None``이 되어서는 안되며, 비교 가능해야합니다.
만약 값이 타입의 인스턴스가 아닌 경우, ``raise``문이 예외 인스턴트를 생성해서 만들듯이 그 값을 통해 새로운 예외 인스터스가 생성됩니다.
만약 traceback이 존재하는 경우, 반드시 유효한 파이썬 traceback 오브젝트여야 합니다. 안그러면 ``TypeError``가 발생합니다.

Note: The name of the ``throw()`` method was selected for several reasons.
``Raise`` is a keyword and so cannot be used as a method name.  Unlike
``raise`` (which immediately raises an exception from the current execution
point), ``throw()`` first resumes the generator, and only then raises the
exception.  The word *throw* is suggestive of putting the exception in
another location, and is already associated with exceptions in other
languages.

알아두세요 : ``throw()``라는 이름이 선택되기까지는 몇가지 이유가 있었습니다.
``Raise``는 이미 keyword이라 메서드 이름으로 쓸 수 없습니다.
``raise`` (와 같이 실행 지점에서 즉시 실행 지점에서 예외를 raise하는 것), ``throw()``는 일단 제너레이터를 재시작하고 예외를 raise합니다.
단어 *throw* 자체에서 발생한 예외를 다른 곳으로 "넘겨준다"는 느낌이 있기도 하며, 다른 프로그래밍 언어에서 사용하고 있다는 점도 이유중 하나입니다.

Alternative method names were considered: ``resolve()``, ``signal()``,
``genraise()``, ``raiseinto()``, and ``flush()``.  None of these seem to fit
as well as ``throw()``.

다른 제안으로는 : ``resolve()``, ``signal()``, ``genraise()``, ``raiseinto()``, ``flush()`` 가 있었지만, 
``throw()``만큼 알맞는 건 없었던 것 같네요.

New standard exception: ``GeneratorExit``
-----------------------------------------

A new standard exception is defined, ``GeneratorExit``, inheriting from
``Exception``.  A generator should handle this by re-raising it (or just not
catching it) or by raising ``StopIteration``.

새로운 표준 예외: ``GeneratorExit``
-----------------------------------------

새로운 표준 예외를 정의했습니다, ``Exception``에서 상속받은 ``GeneratorExit``입니다.
제너레이터는 이 예뢰를 다시 raise (re-raise)하거나(예외를 처리하지 않고 그냥 무시하거나) ``StopIteration``을 raise 함으로써 처리할 수 있습니다.

New generator method: ``close()``
---------------------------------

``g.close()`` is defined by the following pseudo-code::

    def close(self):
        try:
            self.throw(GeneratorExit)
        except (GeneratorExit, StopIteration):
            pass
        else:
            raise RuntimeError("generator ignored GeneratorExit")
        # Other exceptions are not caught

새로운 제너레이터 메서드 : ``close()``
---------------------------------

``g.close()`` 는 다음 슈도 코드를 기반으로 정의됩니다::

    def close(self):
        try:
            self.throw(GeneratorExit)
        except (GeneratorExit, StopIteration):
            pass
        else:
            raise RuntimeError("generator ignored GeneratorExit")
        # Other exceptions are not caught


New generator method: __del__()
-------------------------------

새로운 제너레이터 메서드: __del__()
-------------------------------

``g.__del__()`` is a wrapper for ``g.close()``.  This will be called when
the generator object is garbage-collected (in CPython, this is when its
reference count goes to zero).  If ``close()`` raises an exception, a
traceback for the exception is printed to ``sys.stderr`` and further
ignored; it is not propagated back to the place that triggered the garbage
collection.  This is consistent with the handling of exceptions in
``__del__()`` methods on class instances.

``g.__del__()``는 ``g.close()``를 위한 랩퍼(wrapper)입니다.
이 메서드는 GC 될 떄 호출될 것입니다( CPython에서는 레퍼런스 카운트가 0이 될 때를 의미합니다).
만약 ``close()``가 예외를 raise하는 경우, traceback이 ``sys.stderr``를 통해 출력되며 그 이상의 예외는 무시됩니다.
제너레이터를 GC하도록 한 호출자(caller)까지 전달되지는 않습니다.
클래스 인스턴스의 ``__del__()`` 메서드의 예외 핸들링하는 것과 유사합니다.

If the generator object participates in a cycle, ``g.__del__()`` may not be
called.  This is the behavior of CPython's current garbage collector.  The
reason for the restriction is that the GC code needs to *break* a cycle at
an arbitrary point in order to collect it, and from then on no Python code
should be allowed to see the objects that formed the cycle, as they may be
in an invalid state.  Objects *hanging off* a cycle are not subject to this
restriction.

만약 제너레이터 오브젝트가 사이클(cycle)에서 작동하는 경우, ``g.__del__()``는 호출이 안될수도 있습니다.
이 같은 현상은 CPython의 특성 때문입니다(2005년 당시).
제한이 생긴 이유는 GC가 작동하기 위해서는 사이클을 특정한 시점에 *멈추고* 들어가 "GC"를 해야되는데,
그 시점에 사이클을 생성한 단 하나의 파이썬 코드도 보고(참고)하고 있으면 안되기 때문입니다 - 이게 뭘까..?
사이클과 관련되지 않은 오브젝트는 해당되지 않습니다.

Note that it is unlikely to see a generator object participate in a cycle in
practice.  However, storing a generator object in a global variable creates
a cycle via the generator frame's ``f_globals`` pointer.  Another way to
create a cycle would be to store a reference to the generator object in a
data structure that is passed to the generator as an argument (e.g., if an
object has a method that's a generator, and keeps a reference to a running
iterator created by that method).  Neither of these cases are very likely
given the typical patterns of generator use.

실제로 사이클에서 작동하는 제너레이터 오브젝트를 보기 드물다는 점을 참고하세요.
하지만, 글로벌 변수에 제너레이터 오브젝트를 저장하는 것은 제너레이터 프레임의 ``f_globals`` 포인터를 통해 사이클이 생성됩니다.
사이클을 만들기 위한 다른 방법으로는 제너레이터(A)의 인수로 넘겨주는 데이터 구조에 제너레이터(A)를 참조하는 경우도 있습니다.
이런 방법들은 일반적인 제너레이터를 사용하는 패턴이 아닙니다.

Also, in the CPython implementation of this PEP, the frame object used by
the generator should be released whenever its execution is terminated due to
an error or normal exit.  This will ensure that generators that cannot be
resumed do not remain part of an uncollectable reference cycle.  This allows
other code to potentially use ``close()`` in a ``try/finally`` or ``with``
block (per PEP 343) to ensure that a given generator is properly finalized.

그리고, 이 PEP을 구현하는 CPython에서, 제너레이터가 사용하는 프레임 오브젝트는 제너레이터가 어떤 방식으로든 종료가 되었을 때 해제시킵니다.
덕분에 재시작할 수 없는 제너레이터로 인한 메모리 누수가 발생하지 않게끔 할 수 있습니다.
이러한 특성 때문에 ``try/finally`` 또는 ``with`` 블럭에서 제너레이터를 닫는 ``close()`` 메서드를 굳이 사용하지 않을 수 있습니다.

Optional Extensions
===================

The Extended ``continue`` Statement
-----------------------------------

An earlier draft of this PEP proposed a new ``continue EXPR`` syntax for use
in for-loops (carried over from PEP 340), that would pass the value of
*EXPR* into the iterator being looped over. This feature has been withdrawn
for the time being, because the scope of this PEP has been narrowed to focus
only on passing values into generator-iterators, and not other kinds of
iterators.  It was also felt by some on the Python-Dev list that adding new
syntax for this particular feature would be premature at best.


Open Issues
===========

Discussion on python-dev has revealed some open issues.  I list them here, with
my preferred resolution and its motivation.  The PEP as currently written
reflects this preferred resolution.

1. What exception should be raised by ``close()`` when the generator yields
   another value as a response to the ``GeneratorExit`` exception?

   I originally chose ``TypeError`` because it represents gross misbehavior of
   the generator function, which should be fixed by changing the code.  But the
   ``with_template`` decorator class in PEP 343 uses ``RuntimeError`` for
   similar offenses.  Arguably they should all use the same exception.  I'd
   rather not introduce a new exception class just for this purpose, since it's
   not an exception that I want people to catch: I want it to turn into a
   traceback which is seen by the programmer who then fixes the code.  So now I
   believe they should both raise ``RuntimeError``. There are some precedents
   for that: it's raised by the core Python code in situations where endless
   recursion is detected, and for uninitialized objects (and for a variety of
   miscellaneous conditions).

2. Oren Tirosh has proposed renaming the ``send()`` method to ``feed()``, for
   compatibility with the *consumer interface* (see
   http://effbot.org/zone/consumer.htm for the specification.)

   However, looking more closely at the consumer interface, it seems that the
   desired semantics for ``feed()`` are different than for ``send()``, because
   ``send()`` can't be meaningfully called on a just-started generator.  Also,
   the consumer interface as currently defined doesn't include handling for
   ``StopIteration``.

   Therefore, it seems like it would probably be more useful to create a simple
   decorator that wraps a generator function to make it conform to the consumer
   interface.  For example, it could *warm up* the generator with an initial
   ``next()`` call, trap StopIteration, and perhaps even provide ``reset()`` by
   re-invoking the generator function.


Examples
========

1. A simple *consumer* decorator that makes a generator function automatically
   advance to its first yield point when initially called::

    def consumer(func):
        def wrapper(*args,**kw):
            gen = func(*args, **kw)
            gen.next()
            return gen
        wrapper.__name__ = func.__name__
        wrapper.__dict__ = func.__dict__
        wrapper.__doc__  = func.__doc__
        return wrapper

2. An example of using the *consumer* decorator to create a *reverse generator*
   that receives images and creates thumbnail pages, sending them on to another
   consumer.  Functions like this can be chained together to form efficient
   processing pipelines of *consumers* that each can have complex internal
   state::

    @consumer
    def thumbnail_pager(pagesize, thumbsize, destination):
        while True:
            page = new_image(pagesize)
            rows, columns = pagesize / thumbsize
            pending = False
            try:
                for row in xrange(rows):
                    for column in xrange(columns):
                        thumb = create_thumbnail((yield), thumbsize)
                        page.write(
                            thumb, col*thumbsize.x, row*thumbsize.y )
                        pending = True
            except GeneratorExit:
                # close() was called, so flush any pending output
                if pending:
                    destination.send(page)

                # then close the downstream consumer, and exit
                destination.close()
                return
            else:
                # we finished a page full of thumbnails, so send it
                # downstream and keep on looping
                destination.send(page)

    @consumer
    def jpeg_writer(dirname):
        fileno = 1
        while True:
            filename = os.path.join(dirname,"page%04d.jpg" % fileno)
            write_jpeg((yield), filename)
            fileno += 1


    # Put them together to make a function that makes thumbnail
    # pages from a list of images and other parameters.
    #
    def write_thumbnails(pagesize, thumbsize, images, output_dir):
        pipeline = thumbnail_pager(
            pagesize, thumbsize, jpeg_writer(output_dir)
        )

        for image in images:
            pipeline.send(image)

        pipeline.close()

3. A simple co-routine scheduler or *trampoline* that lets coroutines *call*
   other coroutines by yielding the coroutine they wish to invoke.  Any
   non-generator value yielded by a coroutine is returned to the coroutine that
   *called* the one yielding the value.  Similarly, if a coroutine raises an
   exception, the exception is propagated to its *caller*.  In effect, this
   example emulates simple tasklets as are used in Stackless Python, as long as
   you use a yield expression to invoke routines that would otherwise *block*.
   This is only a very simple example, and far more sophisticated schedulers
   are possible.  (For example, the existing GTasklet framework for Python
   (http://www.gnome.org/~gjc/gtasklet/gtasklets.html) and the peak.events
   framework (http://peak.telecommunity.com/) already implement similar
   scheduling capabilities, but must currently use awkward workarounds for the
   inability to pass values or exceptions into generators.)

   ::

    import collections

    class Trampoline:
        """Manage communications between coroutines"""

        running = False

        def __init__(self):
            self.queue = collections.deque()

        def add(self, coroutine):
            """Request that a coroutine be executed"""
            self.schedule(coroutine)

        def run(self):
            result = None
            self.running = True
            try:
                while self.running and self.queue:
                   func = self.queue.popleft()
                   result = func()
                return result
            finally:
                self.running = False

        def stop(self):
            self.running = False

        def schedule(self, coroutine, stack=(), val=None, *exc):
            def resume():
                value = val
                try:
                    if exc:
                        value = coroutine.throw(value,*exc)
                    else:
                        value = coroutine.send(value)
                except:
                    if stack:
                        # send the error back to the "caller"
                        self.schedule(
                            stack[0], stack[1], *sys.exc_info()
                        )
                    else:
                        # Nothing left in this pseudothread to
                        # handle it, let it propagate to the
                        # run loop
                        raise

                if isinstance(value, types.GeneratorType):
                    # Yielded to a specific coroutine, push the
                    # current one on the stack, and call the new
                    # one with no args
                    self.schedule(value, (coroutine,stack))

                elif stack:
                    # Yielded a result, pop the stack and send the
                    # value to the caller
                    self.schedule(stack[0], stack[1], value)

                # else: this pseudothread has ended

            self.queue.append(resume)

4. A simple *echo* server, and code to run it using a trampoline (presumes the
   existence of ``nonblocking_read``, ``nonblocking_write``, and other I/O
   coroutines, that e.g. raise ``ConnectionLost`` if the connection is
   closed)::

       # coroutine function that echos data back on a connected
       # socket
       #
       def echo_handler(sock):
           while True:
               try:
                   data = yield nonblocking_read(sock)
                   yield nonblocking_write(sock, data)
               except ConnectionLost:
                   pass  # exit normally if connection lost

       # coroutine function that listens for connections on a
       # socket, and then launches a service "handler" coroutine
       # to service the connection
       #
       def listen_on(trampoline, sock, handler):
           while True:
               # get the next incoming connection
               connected_socket = yield nonblocking_accept(sock)

               # start another coroutine to handle the connection
               trampoline.add( handler(connected_socket) )

       # Create a scheduler to manage all our coroutines
       t = Trampoline()

       # Create a coroutine instance to run the echo_handler on
       # incoming connections
       #
       server = listen_on(
           t, listening_socket("localhost","echo"), echo_handler
       )

       # Add the coroutine to the scheduler
       t.add(server)

       # loop forever, accepting connections and servicing them
       # "in parallel"
       #
       t.run()


Reference Implementation
========================

A prototype patch implementing all of the features described in this PEP is
available as SourceForge patch #1223381 (https://bugs.python.org/issue1223381).

This patch was committed to CVS 01-02 August 2005.


Acknowledgements
================

Raymond Hettinger (PEP 288) and Samuele Pedroni (PEP 325) first formally
proposed the ideas of communicating values or exceptions into generators, and
the ability to *close* generators.  Timothy Delaney suggested the title of this
PEP, and Steven Bethard helped edit a previous version.  See also the
Acknowledgements section of PEP 340.

Raymond Hettinger (PEP 288)과 Samuele Pedroni (PEP 325)가 처음으로 소통하는 (communicating ; 제어권을 주고 받는 - 코루틴) 제너레이터의 개념과 *중단*할 수 있는 제너레이터를 제안했습니다.
Timothy Delaney는 이 PEP의 제목을 제안했고, Steven Bethard는 이전 버전을 수정하는데 도움을 줬습니다.
PEP 340의 Acknowledgements를 참고하세요.


References
==========

TBD.

Copyright
=========

This document has been placed in the public domain.


..
   Local Variables:
   mode: indented-text
   indent-tabs-mode: nil
   sentence-end-double-space: t
   fill-column: 70
   coding: utf-8
   End:

                  