# round()

## 목차

* 파이썬 공식 문서 - 한글(번역)

    * float 타입의 round()
    
* 파이썬 공식 문서 - 영어(원문)

## 파이썬 공식 문서 - 한글(번역)

round(number[, ndigits])

주어진 두번째 인자 *ndigits*(정수)를 10^(-ndigits) 정교함으로 반올림한 숫자를 반환합니다.

*ndigits* 인자가 주어지지 않거나, `None`이면 가장 정수로 반올림한 숫자를 반환합니다.

```python
# 10^-(1) = 10의 자리수에서 반올림합니다
round(1.5)  # 2 (int)
round(1.5, None)    # 2 (int)

# 10^-(-1) = 10의 자리수에서 반올림합니다
round(15, -1)   # 20

round(15, -2)   # 0
```

수학시간에 배웠듯이 반올림을 함으로 음수를 `round()`하는 경우 오해하는 일이 없길 바랍니다

```python
round(-0.5) # 0

round(-2.5) # -2
```

For a general Python object number, round delegates to number.\_\_round\_\_.

일반 파이썬 내장 숫자형 타입은 각 타입에 정의된 \_\_round\_\_()가 실행됩니다.

아래와 같이 클래스를 나만의 방식대로 정의한 뒤 사용할 수 있습니다.

```python
class MyNum():
    def __init__(self, data):
        self.data = data
        
    def __round__(self):
        return self.data +1
        
        
s = MyNum(4.5)

round(s)    # 5.5
```

### float 타입의 round()

float, 부동소수점 타입의 경우 예상하지 못한 결과가 나올 수 있습니다.

```python
round(2.675, 2) # 2.67
```

버그가 아니라 정밀도 때문에 나눗셈이 정확하게 이뤄지지 않을 수 있기 때문입니다.

더 자세한 내용은 [Floating Point Arithmetic](https://docs.python.org/3/tutorial/floatingpoint.html#tut-fp-issues)를 참고하시길 바랍니다.

## 파이썬 공식 문서 - 영어(원문)

round(number[, ndigits])

Return *number* rounded to *ndigits* precision after the decimal point.<br>
If *ndigits* is omitted or is `None`, it returns the nearest integer to its input.

For the built-in types supporting [round()](https://docs.python.org/3/library/functions.html#round), values are rounded to the closest multiple of 10 to the power minus *ndigits*; if two multiples are equally close, rounding is done toward the even choice (so, for example, both `round(0.5)` and `round(-0.5)` are `0`, and `round(1.5)` is `2`).<br>
Any integer value is valid for *ndigits*(positive, zero, or negative).<br>
The return value is an integer if *ndigits* is omitted or `None`.<br>
Otherwise the return value has the same type as *number*.

For a general Python object number, round delegates to number.\_\_round\_\_.

'''
Note The behavior of `round()` for floats can be surprising:
for example, `round(2.675, 2)` gives `2.67` instead of the expected `2.68`.
This is not a bug: it’s a result of the fact that most decimal fractions can’t be represented exactly as a float.
See [Floating Point Arithmetic](https://docs.python.org/3/tutorial/floatingpoint.html#tut-fp-issues): Issues and Limitations for more information.
'''