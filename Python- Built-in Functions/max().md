# max()

본 문서는 최댓값, 최솟값을 찾는 차이외에 min().md 내용과 같습니다

## 목차

* 파이썬 공식 문서 - 한글(번역)

    * Keyword-only Arguments
    
        * key
        
        * default
    
* 파이썬 공식 문서 - 영어(원문)

## 파이썬 공식 문서 - 한글(번역)

max(iterable, *[, key, default])
max(arg1, arg2, *args[, key])

이터러블 객체 또는 주어진 두개 이상의 인자들 중 제일 큰 아이템을 반환합니다.

하나의 인자만 주어진 경우 인자는 반드시 [이터러블](https://docs.python.org/3/glossary.html#term-iterable)한 객체야합니다.<br>
이터러블 객체의 가장 큰 아이템을 반환합니다.

```python
# 문자열은 이터러블(iterable)합니다
max('abcde')    # e

# 숫자 자료형은 이터러블하지 않습니다
max(12345)
TypeError: 'int' object is not iterable
```

두개 이상의 인자가 주어진 경우, 가장 큰 인자를 반환합니다.

```python
max(3,4,19) # 19
max('a', 'b', 'c')  # 'c'
```

값이 똑같은 최댓값이 있는 경우, 제일 먼저 찾은 최댓값에 해당하는 객체를 반환합니다.

이런 특징은 max() 메서드와 같은 기능을 하는 `sorted(iterable, key=keyfunc, reverse=True)[0]`와 `heapq.nlargest(1, iterable, key=keyfunc)`도 동일하게 작동합니다.

### Keyword-only Arguments

max() 메서드에는 두개의 키워드 인자가 있습니다.

`max(iterable, *[, key, default])`

keyword-only 인자는 함수의 인자 순서 상관없이 `(인자 이름) = 인자`로 대입하는 인자를 의미합니다.<br>
(여기서는 max( ... , key = '이거' , default = '이거') )

#### 1. key

max() 메서드는 정렬 후 제일 큰 요소를 반환하는 것입니다.<br>
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

파이썬에서 어떤것을 기준으로 정렬할지 모르는 경우, 직접 "**이 값을 기준으로 가지고 정렬하고 최댓값을 반환해!**" 라고하는 것입니다.

`max(list2D, key = MyFunc)`이 어떻게 작동하는지 하나하나 풀어 쓰겠습니다.

    1. max 함수는 이터러블 첫번째 인자에서 하나의 요소(A)들을 순회하며
    
    2. **하나의 인자만을 받는 함수** MyFunc를 호출합니다 : MyFunc(A)
    
    3. MyFunc(A)는 반드시 비교할 수 있는 숫자(int, float, 등) , 문자열, 등을 반환해야합니다
    
    4. max 메서드는 list2D의 모든 요소를 MyFunc에 넣고 돌린 후의 값들 원본과 매핑(mapping) 후 정렬합니다
    
    5. 최댓값에 해당하는 원본 객체(A)을 반환합니다.

```python
# list2D에서 2번째 요소를 기준으로 정렬하는 경우

def foo(x):
    return x[1]

max(list2D, key = foo)  # [5,6,3]

# 람다함수를 이용해 간편하게 사용할 수도 있습니다

max(list2D, key = lambda x : x[1] ) # [5,6,3]
```

같은 방식으로 Student 클래스 인스턴스들의 최댓값을 찾을 수 있습니다.

```python
# 학생의 점수를 기준으로 최댓값을 찾는 경우
max(students, key = lambda x : x.score) # <Student class instance> name : dpo, score : 831

# 학생의 이름을 기준으로 최댓값을 찾는 경우
max(students, key = lambda x : x.name) # <Student class instance> name : rulu, score : 231
```

keyword-only *key* 인자는 [list.sort()](https://docs.python.org/3/library/stdtypes.html#list.sort)의 키워드 인자 *key*와 같은 역할을 합니다.

#### 2. default

*default* 인자는 첫번째 인자로 주어진 이터러블이 비어있는 경우(길이가 0인 경우) 기본값으로 반환할 값을 의미합니다.

```python
max([], default = 10)   # 10

max('', default = 'empty!') # 'empty!'
```

첫번째 인자가 빈 이터러블이고, default 키워드 인자가 없는 경우 [ValueError](https://docs.python.org/3/library/exceptions.html#ValueError)를 던집니다.

```python
max([])
# ValueError: max() arg is an empty sequence

max('')
# ValueError: max() arg is an empty sequence
```

-------

파이썬 버전 3.4 추가:

*defualt* keyword-only 인자가 추가되었습니다.

파이썬 버전 3.8 변화:

*key*의 인자로 `None`을 대입할 수 있습니다.

`max(list2D, key = None, default = [0])`

## 파이썬 공식 문서 - 영어(원문)

max(iterable, *[, key, default])
max(arg1, arg2, *args[,key])

Return the largest item in an iterable or the largest of two or more arguments.

If One positional arguement is provided, it should be an iterable.<br>
The largest item in the iterable is returned.<br>
If two or more positional arguments are provided, the largest of the positional arguments is returned.

There are two optional keyword-only arguments.<br> The key argument specifies a one-argument ordering function like that used for list.sort().<br> The default argument specifies an object to return if the provided iterable is empty.<br> If the iterable is empty and default is not provided, a ValueError is raised.

If multiple items are maximal, the function returns the first one encountered.<br> This is consistent with other sort-stability preserving tools such as `sorted(iterable, key=keyfunc, reverse=True)[0]` and `heapq.nlargest(1, iterable, key = keyfunc).`

New in version 3.4 : 

The default keyword-only argument.

Changed in version 3.8:

The key can be `None`