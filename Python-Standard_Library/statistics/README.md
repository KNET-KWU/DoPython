# statistics - 간단한 통계 도구

`statistics`는 평균(mean), 중앙값(median), 분산(variance)을 구하는 함수가 있는 모듈입니다.

파이썬은 기본적으로 `int`와 `float`를 같은 숫자로 취급하지만,<br>
`float`만으로 채워진 값들을 더 빨리 연산 할 수 있는 `f`로 시작하는 함수들을 제공합니다 (예 : `fsum`).

마찬가지로 `statistics` 모듈에는 `f`로 시작하는 함수들이 정의되어 있습니다.

이 모듈에는 기본적인 통계에 대한 함수들이 정의되어 있습니다.

무엇보다 중요한 점은 내장된 파이썬 모듈은 `cython`으로 작성된 모듈입니다.<br>
아무리 최적화된 Python 코드를 작성해도 내장 모듈의 속도를 따라갈 수 없습니다.

안정성, 논리적 오류, 속도, 등 모든 방면에서 유용하므로 사용하는 것을 추천드립니다!

## 목차

* 평균

    * mean  - 데이터의 평균
    
    * fmean - 부동소수점 데이터의 평균 구하기

    * geometric_mean    - 
    
    * harmonic_mean     - 조화평균

* 중앙값

    * median    - 중앙값
    
    * median_low    - 
    
    * median_high   -
    
    * median_grouped    -
    
    * mode  -
    
    * multimode     -
    
    * quantiles     - 분위(제 n 분위)
    
* 분산

    * stdev     - 표준편차
    
    * variance  - 분산

    * pstdev    - p 표준편차
    
    * pvariance     - p 분산