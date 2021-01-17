# textwrap - function

textwrap 기능을 사용하기 위해서 클래스 인스턴스를 생성할 필요없이 즉시 사용할 수 있는 함수 모음입니다.

**단** textwrap 함수 내부에서 임시 객체를 생성한 뒤에 문자열을 반환하기 때문에 

같은 문자열이 `textwrap`의 기능을 반복적으로 사용해야하는 경우, `TextWrapper` 클래스 인스턴스를 만든 뒤에 사용하시길 바랍니다.

## 목차
    
    * wrap
    
    * fill
    
    * shorten
    
    * dedent
    
    * indent
    
### wrap

`textwrap.wrap(text, width = 70, **kwargs) -> list[str]`

주어진 첫번째 인자 *text*를 정수인 두번째 인자 *width* 만큼의 길이로 나눈 여러 줄의 문장을 반환합니다.

한 줄씩 나눈 문장으로 이루어진 문자열이 담긴 리스트가 반환됩니다.

```python
from textwrap import wrap

text = """
The Zen of Python, by Tim Peters

Beautiful is better than ugly. Explicit is better than implicit. Simple is better than complex. Complex is better than complicated.  Flat is better than nested. Sparse is better than dense. Readability counts. Special cases aren't special enough to break the rules. Although practicality beats purity. Errors should never pass silently. Unless explicitly silenced. In the face of ambiguity, refuse the temptation to guess.
"""

width70 = '-'*70

for line in textwrap.wrap(text, width = 70):
    print(line)
    print(width70)
```

예시 코드의 *text*는 `import this`를 했을 때 출력되는 내용의 일부를 한 줄로 만든 것입니다.

width70은 길이 70의 문자열입니다.

출력 : 

```python
 The Zen of Python, by Tim Peters  Beautiful is better than ugly.
----------------------------------------------------------------------
Explicit is better than implicit. Simple is better than complex.
----------------------------------------------------------------------
Complex is better than complicated.  Flat is better than nested.
----------------------------------------------------------------------
Sparse is better than dense. Readability counts. Special cases aren't
----------------------------------------------------------------------
special enough to break the rules. Although practicality beats purity.
----------------------------------------------------------------------
Errors should never pass silently. Unless explicitly silenced. In the
----------------------------------------------------------------------
face of ambiguity, refuse the temptation to guess.
----------------------------------------------------------------------
```

문장의 마지막 단어가 *width*를 초과했을 경우, 다음 문장으로 줄을 바꾼 것을 볼 수 있습니다.

일반 문서 편집기를 통해 작성했을 때처럼 작동합니다.

<br>

### fill

`textwrap.fill(text, width=70, **kwargs) -> str`

`textwrap.wrap` 함수가 사용되고나서 반환한 문자열 리스트를 '\n'`으로 구분한 하나의 문자열을 반환합니다.

정리하자면, 

`textwrap.wrap` 함수는 주어진 길이(두번째 인자 *width*)로 문장을 나눈 것들을 모은 **리스트**로 반환하는데

`textwrap.fill` 함수는 나뉜 문장들을 '\n'을 구분자로 합친 **하나의 문자열**을 반환합니다.

사용 방법이 아래의 예시 코드처럼 다릅니다.

```python
# 한줄씩 출력하는 경우 아래의 출력은 같습니다

# textwrap.wrap
for line in textwrap.wrap(text):
    print(line)

# textwrap.fill
print(textwrap.fill(text))
```

코드로 이해를 하자면

`'\n'.join(wrap(text, ...))` 와 똑같이 작동합니다. 

<br>

### shorten

`textwrap.shorten(text, width, **kwargs) -> str`

주어진 첫번째 인자 *text*의 길이가 주어진 두번째 인자 *width*보다 초과하는 경우 축약된 문자(기본값 : `[...]`)로 바뀐 문자열을 반환합니다.

```python
from textwrap import shorten

# 1. 문장 길이가 width보다 작으면 그대로 반환
print(shorten("I am ccppoo", width = 15))   # 'I am ccppoo'

# 2. 문장 길이가 width보다 작으면 길이를 초과하는 마지막 단어를 포함한 채 생략
print(shorten("I am ccppoo", width = 10))   # 'I am [...]'

# 3. 생략문자의 기본값 '[...]' 대신 다른것도 사용가능
print(shorten("I am ccppoo", width = 10, placeholder = '생략'))   # 'I am (생략)'
```

공백문자 중 대표적으로 탭(\t) 같이 일반 스페이스(whitespace)보다 길이가 긴(외관상) 공백문자는 축약되기 전에 모두 스페이스로 치환됩니다.

공백문자 자체의 길이를 걱정할 필요 없습니다.

```python
print('I am ccppoo')

print('I\tam\tccppoo')

print(shorten("I\tam\tccppoo", width = 10))   # 'I am [...]'
```

<br>

### dedent

`textwrap.dedent(text) -> str`

문단의 첫번째 문장 앞에 여백을 의미하는 **indent**의 없애는(de~) 반대 의미를 가진 함수입니다.

'\n'로 구분되는 각 문장 첫부분에 있는 여백들을 제거한 문장을 반환합니다.

```python
s = '''\
  object
  hello 
  world
'''
print(repr(s))          # '  object\n  hello \n  world\n'
print(repr(dedent(s)))  # 'object\nhello \nworld\n'
```

<br>

### indent

`textwrap.indent(text, prefix, predicate=None) -> str`

`dedent` 함수와 반대로 인덴트(indent)를 만들어주는 함수입니다.

textwrap 모듈 중에서 사용자들이 콘솔창에서 출력 메세지를 읽을만하게 출력하기 위해서 이 함수가 가장 유용하게 쓰이지 않을까 싶네요.

우선 동작하는 순서는 다음과 같습니다.

첫번째 인자 *text*는 문장을 '\n'을 기준으로 나누는 문자열(str) 내장메서드 `text.splitlines(True)`로 문장이 나뉩니다(리스트로 반환).

리스트에 담긴 문장마다 앞부분에 두번째 인자 *prefix*와 함께 출력합니다.

```python
s = '''\
    I am ccppoo
    I want to go home
    let me go home
'''

print(indent(s, '> ')
```

출력 : 

```python
>     I am ccppoo
>     I want to go home
>     let me go home
```

하나의 인자(문장)를 받는 세번째 인자 *predicate*는 나뉜 문장이 `True`를 반환하는 경우<br>
두번째 인자 `prefix`를 출력합니다.

```python
print(indent(s, '> ', lambda line : line.lstrip().startswith('I') ))
```

`lstrip()`한 이유는 각 문장 앞에 `\t`(tab)이 있기 때문입니다.

출력 : 

```
>     I am ccppoo
>     I want to go home
    let me go home
'''
```

활용 예시
  
```python
from textwrap import indent, wrap

text = 'Warning! Error : ...'

a = indent(text, '<Need Attention>'.center(40) + '\n\n', lambda line : line.lower().find('warning') != -1)

print(a)
```

이런식으로 에러메세지를 출력할 때 만들면 눈에 더 잘들어오지 않을까요?