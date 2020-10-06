# reversed()

## 목차

* 파이썬 공식 문서 - 한글(번역)
    
* 파이썬 공식 문서 - 영어(원문)

## 파이썬 공식 문서 - 한글(번역)

reversed(seq)

인자로 주어진 이터레이터를 역순으로 **새로** 만들어 반환합니다.

```python
li = [x for x in range(10)]

f'li : {li}'    # [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

reversed_list = list(reversed(li))

reversed_list   # [9, 8, 7, 6, 5, 4, 3, 2, 1, 0]

# 기존의 li는 변하지 않습니다
li  # [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
```

객체는 \_\_reversed\_\_() 메서드가 정의되어 있거나, 시퀀스(sequence)프로토콜을 지원해야합니다.
(시퀀스 프로토콜은 \_\_len\_\_() 메서드와 \_\_getitem\_\_() 메서드가 정의되어있습니다.)

혼동할 수 있는 리스트(list) 자체를 역순으로 반환하는 메서드는 다음과 같습니다.

```python
li = [x for x in range(10]

li # [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

li.reverse()

li  # [9, 8, 7, 6, 5, 4, 3, 2, 1, 0]
```

## 파이썬 공식 문서 - 영어(원문)

reversed(seq)

Return a reverse [iterator](https://docs.python.org/3/glossary.html#term-iterator)<br>
*seq* must be an object which has a [\_\_reversed\_\_()](https://docs.python.org/3/reference/datamodel.html#object.__reversed__) method or supports the sequence protocol (the [\_\_len\_\_()](https://docs.python.org/3/reference/datamodel.html#object.__len__) method and the [\_\_getitem\_\_()](https://docs.python.org/3/reference/datamodel.html#object.__getitem__) method with integer arguments starting at `0`).
