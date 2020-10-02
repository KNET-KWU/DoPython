# print()

## 목차

* 파이썬 공식 문서 - 한글(번역)
    
* 파이썬 공식 문서 - 영어(원문)

## 파이썬 공식 문서 - 한글(번역)

print(*objects, sep=' ', end='\n', file=sys.stdout, flush=False)

문자열 스트림 파일에 객체를 출력합니다.<br>
객체들은 *sep* 인자로 구분되어 출력되고, 마지막 문자는 *end* 인자로 주어진 값을 출력합니다.

```python
# 문자열도 객체입니다 - !

# sep의 기본값은 ' '(공백 하나), end의 기본값은 줄바꿈 '\n' 입니다
print('ccppoo', 'is', 'doing', 'python')    # ccppoo is doing python

print('ccppoo', 'is', 'doing', 'python', sep = '@', end='$')    # ccppoo@is@doing@python$
```

print() 메서드의 키워드 *sep*, *end*, *file*, *flush* 를 사용하려면 키워드와 함께 써야합니다.<br>
(*file*, *flush*는 아래에서 다룹니다)

```python
# 엥? 딱봐도 이상하죠?
print('ccppoo', 'is', 'doing', 'python', '@', '$')   # ccppoo is doing python @ $
```

키워드 없이 주어진 인자는 모두 [str()](https://docs.python.org/3/library/stdtypes.html#str) 함수를 통해 문자열로 변환되고 스트림에 출력됩니다.

Both *sep* and end must be strings; they can also be `None`, which means to use the default values.<br>

출력하려는 *sep*과 *end* 키워드 인자는 문자열 클래스(str) 또는 문자열(str)을 상속한 클래스만 쓸 수 있습니다.

\_\_str\_\_ 또는 \_\_repr\_\_ 메서드가 정의된 클래스여도 안됩니다.

```python
class MyClass:
    def __repr__(self):
        return ' =C from __repr__= '
        
    def __str__(self):
        return ' =C from __str__= '

print('Hi', 'ccppoo',end = MyClass(), sep = MyClass())
# TypeError: sep must be None or a string, not MyClass
```

그래도 어떻게든 쓰고 싶다면 문자열 클래스를 상속한 클래스를 사용하면 가능합니다

```python
class MyClass(str):
    def __repr__(self):
        return ' =C from __repr__= '
        
    def __str__(self):
        return ' =C from __str__= '

print('Hi', 'ccppoo',end = MyClass(), sep = MyClass()) # Hi =C from __str__= ccppoo =C from __str__= 
```

아무것도 print() 메서드 인자에 주어지지 않으면, *end*의 기본값(줄바꿈 - '\n')만 출력됩니다.

### file 인자

*file* 인자는 `write(문자열)` 형태의 메서드이어야 합니다.

*file* 키워드 인자에 값을 대입하지 않거나 `None`을 대입하는 경우 (sys.stdout)[https://docs.python.org/3/library/sys.html#sys.stdout]이 사용됩니다.

### print() 메서드를 이용해서 파일에 출력하기

```python
# mode; a : 이어쓰기, w: 새로 쓰기
with open('sample.txt', 'w') as f:
    print('Using Print func!', file = f) # 파이썬 콘솔에 출력되지 않습니다.
    
with open('sample.txt', 'r') as f:
    print('read file\n\n')
    for x in f.readlines():
        print(x) # Using Print func!
```

위 예시처럼 파일에 문자 혹은 이진 데이터를 작성할 경우 `file.write(...)` 메서드를 이용하는 것을 권장합니다.

### sys.stdout과 출력 버퍼

콘솔/화면에 내가 출력하려는 문자가 출력되려면 일단 그 문자를 저장해야하는 파일에 저장되어야한다.

화면으로 출력되기 까지 잠시 문자열들이 대기하는 파일정도로 생각하면된다.

출력된 인자들이 텍스트 문자열로 변환되므로, print() 메서드는 이진파일 객체(binary mode file objects)를 file의 인자로 쓸 수 없습니다.

```python
# 그냥 파일로 열었을 때
with open('sample.txt', 'a') as f:
    print('hello', file = f)    # 문제 없음

# 이진파일로 파일을 열었을 때
with open('sample.txt', 'ab') as f:
    print('hello', file = f)    

TypeError: a bytes-like object is required, not 'str'
```

### flush 인자

출력이 버퍼에 저장됐는지 여부는 *file*의 특성에 따라 결정됩니다.

flush 키워드 인자가 `True`인 경우 *file* 종류 상관없이 강제로 스트림이 flush 됩니다.

화면상으로 출력되기 전에 버퍼에 임시로 저장됩니다.

flush의 True, False 차이를 확연히 알아볼 수 있는 방법을 못찾아서 예시를 못찾겠다.

`flush = True`를 할 때는 

* 반드시 출력이 이루어지고 다음 작업이 수행해야하는 경우

밖에 떠오르지 않는다. 메모리를 공유하는 멀티스레딩을 하는 경우 필요해보인다.

------

파이썬 버전 3.3 변화: 

*flush* 키워드 인자를 추가했습니다.

## 파이썬 공식 문서 - 영어(원문)

print(*objects, sep=' ', end='\n', file=sys.stdout, flush=False)

Print *objects* to the text stream *file*, separated by *sep* and followed by *end*. *sep*, *end*, *file* and *flush*, if present, must be given as keyword arguments.

All non-keyword arguments are converted to strings like [str()](https://docs.python.org/3/library/stdtypes.html#str) does and written to the stream, separated by *sep* and followed by *end*.<br>
Both *sep* and *end* must be strings; they can also be `None`, which means to use the default values.<br>
If no *objects* are given, [print()](https://docs.python.org/3/library/functions.html#print) will just write end.

The *file* argument must be an object with a `write(string)` method; if it is not present or `None`, (sys.stdout)[https://docs.python.org/3/library/sys.html#sys.stdout] will be used.<br>
Since printed arguments are converted to text strings, [print()](https://docs.python.org/3/library/functions.html#print) cannot be used with binary mode file objects.<br>
For these, use `file.write(...)` instead.

Whether output is buffered is usually determined by *file*, but if the *flush* keyword argument is true, the stream is forcibly flushed.

Changed in version 3.3: 

Added the flush keyword argument.