# min()

본 문서는 최댓값, 최솟값을 찾는 차이외에 max().md 내용과 같습니다

## 목차

* 파이썬 공식 문서 - 한글(번역)

    * Keyword-only Arguments
    
        * key
        
        * default
    
* 파이썬 공식 문서 - 영어(원문)

## 파이썬 공식 문서 - 한글(번역)

min(iterable, *[, key, default])
min(arg1, arg2, *args[, key])

이터러블 객체 또는 주어진 두개 이상의 인자들 중 제일 작은 아이템을 반환합니다.

하나의 인자만 주어진 경우 인자는 반드시 [이터러블](https://docs.python.org/3/glossary.html#term-iterable)한 객체야합니다.<br>
이터러블 객체의 가장 작은 아이템을 반환합니다.

```python
# 문자열은 이터러블(iterable)합니다
min('abcde')    # a

# 숫자 자료형은 이터러블하지 않습니다
min(12345)
TypeError: 'int' object is not iterable
```

두개 이상의 인자가 주어진 경우, 가장 작은 인자를 반환합니다.

```python
min(3,4,19) # 3
min('a', 'b', 'c')  # 'a'
```

값이 똑같은 최솟값이 있는 경우, 제일 먼저 찾은 최솟값에 해당하는 객체를 반환합니다.

이런 특징은 min() 메서드와 같은 기능을 하는 `sorted(iterable, key=keyfunc)[0]`와 `heapq.nsmallest(1, iterable, key=keyfunc)`도 동일하게 작동합니다.

### Keyword-only Arguments

min() 메서드에는 두개의 키워드 인자가 있습니다.

`min(iterable, *[, key, default])`

keyword-only 인자는 함수의 인자 순서 상관없이 `(인자 이름) = 인자`로 대입하는 인자를 의미합니다.<br>
(여기서는 min( ... , key = '이거' , default = '이거') )

#### 1. key

min() 메서드는 정렬 후 제일 작은 요소를 반환하는 것입니다.<br>
그래서 key는 주어진 이터러블들의 요소 중 **어떤것을 기준**으로 '정렬'할지 묻는 기준을 의미합니다.

이터러블에는 단순히 인자만 있는 1차원 리스트가 있을 수 있지만,

```python
sort_this = [3,1,5,7,2]
```

이터러블안에 이터러블이 존재할 수도 있고, 객체도 존재할 수 있기 때문에

```python
list2D = [
    [8,4,7],
    [5,6,3],
    [1,2,9]
]

class Student:
    def __init__(self, name, score):
        self.name = name
        self.score = score
    def __repr__(self):
        return f'<Student class instance> name : {self.name}, score : {self.score}'

students = [
    Student('ccppoo', 231),
    Student('dpo', 831),
    Student('kurururu', 314),
    Student('rulu',231)
]
```

파이썬에서 어떤것을 기준으로 정렬할지 모르는 경우, 직접 "**이 값을 기준으로 가지고 정렬하고 최솟값을 반환해!**" 라고하는 것입니다.

`min(list2D, key = MyFunc)`이 어떻게 작동하는지 하나하나 풀어 쓰겠습니다.

    1. min 함수는 이터러블 첫번째 인자에서 하나의 요소(A)들을 순회하며
    
    2. **하나의 인자만을 받는 함수** MyFunc를 호출합니다 : MyFunc(A)
    
    3. MyFunc(A)는 반드시 비교할 수 있는 숫자(int, float, 등) , 문자열, 등을 반환해야합니다
    
    4. min 메서드는 list2D의 모든 요소를 MyFunc에 넣고 돌린 후의 값들 원본과 매핑(mapping) 후 정렬합니다
    
    5. 최솟값에 해당하는 원본 객체(A)을 반환합니다.

```python
# list2D에서 2번째 요소를 기준으로 정렬하는 경우

def foo(x):
    return x[1]

min(list2D, key = foo)  # [1,2,9]

# 람다함수를 이용해 간편하게 사용할 수도 있습니다

min(list2D, key = lambda x : x[1] ) # [1,2,9]
```

같은 방식으로 Student 클래스 인스턴스들의 최솟값을 찾을 수 있습니다.

```python
# 학생의 점수를 기준으로 최솟값을 찾는 경우
min(students, key = lambda x : x.score) # <Student class instance> name : ccppoo, score : 231

# 학생의 이름을 기준으로 최솟값을 찾는 경우
min(students, key = lambda x : x.name) # <Student class instance> name : ccppoo, score : 231
```

keyword-only *key* 인자는 [list.sort()](https://docs.python.org/3/library/stdtypes.html#list.sort)의 키워드 인자 *key*와 같은 역할을 합니다.

#### 2. default

*default* 인자는 첫번째 인자로 주어진 이터러블이 비어있는 경우(길이가 0인 경우) 기본값으로 반환할 값을 의미합니다.

```python
min([], default = 10)   # 10

min('', default = 'empty!') # 'empty!'
```

첫번째 인자가 빈 이터러블이고, default 키워드 인자가 없는 경우 [ValueError](https://docs.python.org/3/library/exceptions.html#ValueError)를 던집니다.

```python
min([])
# ValueError: min() arg is an empty sequence

min('')
# ValueError: min() arg is an empty sequence
```

-------

파이썬 버전 3.4 추가:

*defualt* keyword-only 인자가 추가되었습니다.

파이썬 버전 3.8 변화:

*key*의 인자로 `None`을 대입할 수 있습니다.

`min(list2D, key = None, default = [0])`

## 파이썬 공식 문서 - 영어(원문)

min(iterable, *[, key, default])
min(arg1, arg2, *args[, key])

Return the smallest item in an iterable or the smallest of two or more arguments.

If one positional argument is provided, it should be an [iterable](https://docs.python.org/3/glossary.html#term-iterable).<br>
The smallest item in the iterable is returned.<br>
If two or more positional arguments are provided, the smallest of the positional arguments is returned.

There are two optional keyword-only arguments.<br>
The *key* argument specifies a one-argument ordering function like that used for [list.sort()](https://docs.python.org/3/library/stdtypes.html#list.sort).<br>
The *default* argument specifies an object to return if the provided iterable is empty.<br>
If the iterable is empty and *default* is not provided, a [ValueError](https://docs.python.org/3/library/exceptions.html#ValueError) is raised.

If multiple items are minimal, the function returns the first one encountered.<br>
This is consistent with other sort-stability preserving tools such as `sorted(iterable, key=keyfunc)[0]` and  `heapq.nsmallest(1, iterable, key=keyfunc)`.

New in version 3.4:

The *defualt* keyword-only argument.

Changed in version 3.8:

The *key* can be `None`.