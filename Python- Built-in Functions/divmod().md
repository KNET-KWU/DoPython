# divmod()

두개의 숫자를 인자로 받아, `(몫, 나머지)` 형태의 튜플을 반환하는 메서드입니다.

복소수는 지원하지 않습니다.

divmod은 div + mod의 합성어로

div은 나눗셈을 의미하는 명사 **division**을 의미하고

mod는 나머지 연산을 의미하는 명사 **modulo**를 의미합니다.

원래 컴퓨터에서 나눗셈이란 몫만 가져오는 기능을 의미하므로, 몫과 나머지의 튜플을 반환하는 divmod()라는 이름은 근본이 있네요.

## 목차

* 파이썬 공식 문서 - 한글(번역)
    
* 파이썬 공식 문서 - 영어(원문)


## 파이썬 공식 문서 - 한글(번역)

divmod(a, b)

복소수가 아닌 두개의 숫자를 인자로 받고, a를 b로 나눈 몫과 나머지로 구성된 튜플을 반환합니다.

두 인자가 모두 정수인 경우 다음과 같은 튜플을 반환합니다: `(a//b, a%b)`

나누는 숫자가 부동소수점 수일 경우 : `(q, a%b)`<br>
q는 `math.floor(a/b)`이며, 오차는 1보다 작습니다.

대부분의 경우 `q * b + a % b`는 a에 근접합니다.

```python
divmod(13, 4) # (3, 1)

divmod(123.45, 34) # (3.0, 21.450000000000003)
```

`a % b`가 0이 아닌 경우 b와 같은 부호를 가지며, `0 <= abs(a%b) < abs(b)` 를 만족합니다.

```python
divmod(-10, 3) # (-4, 2)

'''
-10 == -4 * 3 + 2 이런식으로 나눗셈을 처리합니다.
'''
```

## 파이썬 공식 문서 - 영어(원문)

divmod(a, b)

Take two (non complex) numbers as arguments and return a pair of numbers consisting of their quotient and remainder when using integer division.<br>
With mixed operand types, the rules for binary arithmetic operators apply.<br>
For integers, the result is the same as `(a//b, a%b)`.<br>
For floating point numbers the result is `(q, a%b)` where q is usually `math.floor(a/b)` but may be 1 less than that.<br>
In any case `q * b + a % b` is very close to a, if `a % b` is non-zero it has the same sign as b, and `0 <= abs(a%b) < abs(b)`.