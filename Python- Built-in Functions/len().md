# len()

## 목차

* 파이썬 공식 문서 - 한글(번역)
    
* 파이썬 공식 문서 - 영어(원문)

## 파이썬 공식 문서 - 한글(번역)

len(s)

객체의 길이 또는 갖고 있는 아이템의 개수를 반환합니다.

인자 `s`는 문자열, 튜플, 리스트, 등과 같은 *시퀀스*, 또는 딕셔너리, 세트, 프로즌 세트 같은 *컬렉션* 타입이 올 수 있습니다.

대표적으로 문자열은 문자열의 문자 수, 튜플, 리스트, 딕셔너리는 요소의 개수를 반환합니다.

```python
len('ccppoo')   # 6

len(('tu', 'pl', 'e'))  # 3

len({'a' : 1,'b' : 2, 'c' : 3}) # 3
```

클래스 내부에서 정의한 정수를 반환하는 \_\_len\_\_() 메서드를 이용해 len(()을 호출할 수 있습니다

```python
class MyClass:
    def __init__(self, data : int):
    self.data = data
    
    # 길이 혹은 개수와 상관없는 값을 반환해도 상관 없습니다
    def __len___(self) -> int:
        return self.data

len(MyClass(30))    # 30
```

## 파이썬 공식 문서 - 영어(원문)

len(s)

Return the length (the number of items) of an object.<br>
The argument may be a sequence (such as a string, bytes, tuple, list, or range) or a collection (such as a dictionary, set, or frozen set).