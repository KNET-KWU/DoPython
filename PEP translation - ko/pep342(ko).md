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

1. Redefine ``yield`` to be an expression, rather than a statement. The current
   yield statement would become a yield expression whose value is thrown away.
   A yield expression's value is ``None`` whenever the generator is resumed by
   a normal ``next()`` call.

2. Add a new ``send()`` method for generator-iterators, which resumes the
   generator and *sends* a value that becomes the result of the current
   yield-expression.  The ``send()`` method returns the next value yielded by
   the generator, or raises ``StopIteration`` if the generator exits without
   yielding another value.

3. Add a new ``throw()`` method for generator-iterators, which raises an
   exception at the point where the generator was paused, and which returns the
   next value yielded by the generator, raising ``StopIteration`` if the
   generator exits without yielding another value.  (If the generator does not
   catch the passed-in exception, or raises a different exception, then that
   exception propagates to the caller.)

4. Add a ``close()`` method for generator-iterators, which raises
   ``GeneratorExit`` at the point where the generator was paused.  If the
   generator then raises ``StopIteration`` (by exiting normally, or due to
   already being closed) or ``GeneratorExit`` (by not catching the exception),
   ``close()`` returns to its caller.  If the generator yields a value, a
   ``RuntimeError`` is raised.  If the generator raises any other exception, it
   is propagated to the caller. ``close()`` does nothing if the generator has
   already exited due to an exception or normal exit.

5. Add support to ensure that ``close()`` is called when a generator iterator
   is garbage-collected.

6. Allow ``yield`` to be used in ``try/finally`` blocks, since garbage
   collection or an explicit ``close()`` call would now allow the ``finally``
   clause to execute.

A prototype patch implementing all of these changes against the current Python
CVS HEAD is available as SourceForge patch #1223381
(https://bugs.python.org/issue1223381).


Specification: Sending Values into Generators
=============================================

New generator method: ``send(value)``
-------------------------------------

A new method for generator-iterators is proposed, called ``send()``.  It
takes exactly one argument, which is the value that should be *sent in* to
the generator.  Calling ``send(None)`` is exactly equivalent to calling a
generator's ``next()`` method.  Calling ``send()`` with any other value is
the same, except that the value produced by the generator's current
yield expression will be different.

Because generator-iterators begin execution at the top of the generator's
function body, there is no yield expression to receive a value when the
generator has just been created.  Therefore, calling ``send()`` with a
non-``None`` argument is prohibited when the generator iterator has just
started, and a ``TypeError`` is raised if this occurs (presumably due to a
logic error of some kind).  Thus, before you can communicate with a
coroutine you must first call ``next()`` or ``send(None)`` to advance its
execution to the first yield expression.

As with the ``next()`` method, the ``send()`` method returns the next value
yielded by the generator-iterator, or raises ``StopIteration`` if the
generator exits normally, or has already exited.  If the generator raises an
uncaught exception, it is propagated to ``send()``'s caller.

New syntax: Yield Expressions
-----------------------------

The yield-statement will be allowed to be used on the right-hand side of an
assignment; in that case it is referred to as yield-expression.  The value
of this yield-expression is ``None`` unless ``send()`` was called with a
non-``None`` argument; see below.

A yield-expression must always be parenthesized except when it occurs at the
top-level expression on the right-hand side of an assignment.  So

::

    x = yield 42
    x = yield
    x = 12 + (yield 42)
    x = 12 + (yield)
    foo(yield 42)
    foo(yield)

are all legal, but

::

    x = 12 + yield 42
    x = 12 + yield
    foo(yield 42, 12)
    foo(yield, 12)

are all illegal.  (Some of the edge cases are motivated by the current
legality of ``yield 12, 42``.)

Note that a yield-statement or yield-expression without an expression is now
legal.  This makes sense: when the information flow in the ``next()`` call
is reversed, it should be possible to yield without passing an explicit
value (``yield`` is of course equivalent to ``yield None``).

When ``send(value)`` is called, the yield-expression that it resumes will
return the passed-in value.  When ``next()`` is called, the resumed
yield-expression will return ``None``.  If the yield-expression is a
yield-statement, this returned value is ignored, similar to ignoring the
value returned by a function call used as a statement.

In effect, a yield-expression is like an inverted function call; the
argument to yield is in fact returned (yielded) from the currently executing
function, and the *return value* of yield is the argument passed in via
``send()``.

Note: the syntactic extensions to yield make its use very similar to that in
Ruby.  This is intentional.  Do note that in Python the block passes a value
to the generator using ``send(EXPR)`` rather than ``return EXPR``, and the
underlying mechanism whereby control is passed between the generator and the
block is completely different.  Blocks in Python are not compiled into
thunks; rather, ``yield`` suspends execution of the generator's frame.  Some
edge cases work differently; in Python, you cannot save the block for later
use, and you cannot test whether there is a block or not. (XXX - this stuff
about blocks seems out of place now, perhaps Guido can edit to clarify.)


Specification: Exceptions and Cleanup
=====================================

Let a generator object be the iterator produced by calling a generator
function.  Below, *g* always refers to a generator object.

New syntax: ``yield`` allowed inside ``try-finally``
----------------------------------------------------

The syntax for generator functions is extended to allow a yield-statement
inside a ``try-finally`` statement.

New generator method: ``throw(type, value=None, traceback=None)``
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

The effect of raising the exception is exactly as if the statement::

    raise type, value, traceback

was executed at the suspension point.  The type argument must not be
``None``, and the type and value must be compatible.  If the value is not an
instance of the type, a new exception instance is created using the value,
following the same rules that the ``raise`` statement uses to create an
exception instance.  The traceback, if supplied, must be a valid Python
traceback object, or a ``TypeError`` occurs.

Note: The name of the ``throw()`` method was selected for several reasons.
``Raise`` is a keyword and so cannot be used as a method name.  Unlike
``raise`` (which immediately raises an exception from the current execution
point), ``throw()`` first resumes the generator, and only then raises the
exception.  The word *throw* is suggestive of putting the exception in
another location, and is already associated with exceptions in other
languages.

Alternative method names were considered: ``resolve()``, ``signal()``,
``genraise()``, ``raiseinto()``, and ``flush()``.  None of these seem to fit
as well as ``throw()``.

New standard exception: ``GeneratorExit``
-----------------------------------------

A new standard exception is defined, ``GeneratorExit``, inheriting from
``Exception``.  A generator should handle this by re-raising it (or just not
catching it) or by raising ``StopIteration``.

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

New generator method: __del__()
-------------------------------

``g.__del__()`` is a wrapper for ``g.close()``.  This will be called when
the generator object is garbage-collected (in CPython, this is when its
reference count goes to zero).  If ``close()`` raises an exception, a
traceback for the exception is printed to ``sys.stderr`` and further
ignored; it is not propagated back to the place that triggered the garbage
collection.  This is consistent with the handling of exceptions in
``__del__()`` methods on class instances.

If the generator object participates in a cycle, ``g.__del__()`` may not be
called.  This is the behavior of CPython's current garbage collector.  The
reason for the restriction is that the GC code needs to *break* a cycle at
an arbitrary point in order to collect it, and from then on no Python code
should be allowed to see the objects that formed the cycle, as they may be
in an invalid state.  Objects *hanging off* a cycle are not subject to this
restriction.

Note that it is unlikely to see a generator object participate in a cycle in
practice.  However, storing a generator object in a global variable creates
a cycle via the generator frame's ``f_globals`` pointer.  Another way to
create a cycle would be to store a reference to the generator object in a
data structure that is passed to the generator as an argument (e.g., if an
object has a method that's a generator, and keeps a reference to a running
iterator created by that method).  Neither of these cases are very likely
given the typical patterns of generator use.

Also, in the CPython implementation of this PEP, the frame object used by
the generator should be released whenever its execution is terminated due to
an error or normal exit.  This will ensure that generators that cannot be
resumed do not remain part of an uncollectable reference cycle.  This allows
other code to potentially use ``close()`` in a ``try/finally`` or ``with``
block (per PEP 343) to ensure that a given generator is properly finalized.


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

