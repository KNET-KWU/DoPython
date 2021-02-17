# filecmp - 파일이 같은지 다른지 비교할 때

파일의 메타의 데이터를 통해 빠르게 파일을 비교할 수 있거나

파일 자체 데이터를 통해 비교할 수 있습니다.

디렉토리(Directory) 자체도 파일이지만, 혼동을 방지하기 위해서 파일과 디렉토리를 구분해서 작성했습니다

## 목록

* functions

    * filecmp.cmp   - 파일 vs 파일

    * filecmp.cmpfiles - 디렉토리 vs 디렉토리

    * filecmp.clear_cache   - 캐시 지우기

* class

    * filecmp.dircmp    - 디렉토리 vs 디렉토리 (다양한 조건으로 비교하기)

## functions

### filecmp.cmp

`cmp(f1, f2, shallow = True) -> Bool`

shallow = True인 경우

os.stat() 을 이용해서 파일의 메타 데이터를 이용한 비교를 한다.

False인 경우 직접 파일을 이진 모드로 읽어 들여서 비교를 한다.

<br>

### filecmp.cmpfiles

`cmpfiles(dir1, dir2, common, shallow = True) -> (list, list, list)`

디렉토리 경로인 첫번째와 두번째 인자 *dir1*와 *dir2*를 비교합니다.

`filecmp.cmp` 함수와 마찬가지로 세번째 인자 *shallow*의 기본값은 `True`이며, 파일 비교를 할 때 파일의 메타데이터를 비교합니다.

`False`의 경우 직접 파일 바이트 데이터를 읽으며 비교를 합니다.

세번째 키워드 인자 *common*에는 비교할 디렉토리 내부의 파일 이름이 포함된 **리스트(list)**를 받습니다

상대 경로 **'./MyApp/temp'**와 **'./YourApp/temp'**에서 'C.txt'라는 파일과 'Secret'이라는 디렉토리 내부의 'password.txt'라는 파일을 비교하는 예시:

```python
from filrcmp import cmpfiles

# 리스트 3개의 튜플이 반환되므로 이렇게 언패킹하면 됩니다
match, mismatch,  errors = cmpfiles('./MyApp/temp', './YourApp/temp', ['C.txt', 'Secret/password.txt'])
```

**반환값의 내용은 아래와 같습니다**

파일의 이름이 담긴 **3개의 리스트(list)**가 담긴 튜플(tuple)을 반환합니다.

첫번째 리스트 **match** : 같은 파일 이름이 담겨있습니다

두번째 리스트 **mismatch** : 같지 않은 파일 이름이 담겨있는 리스트

세번째 리스트 **errors** : 파일이 존재하지 않거나, 파일 읽기 권한이 없거나, 에러가 발생한 경우

<br>

### filecmp.clear_cache

`clear_cache()`

filecmp 함수가 실행되고 난 후 캐시에 저장되므로 비교하는 파일이 변경될 가능성이 있는 경우 캐시를 지우는 함수입니다.

```python

cache example

```

--------

## class

### dircmp

`dircmp(a, b, ignore = None, hide = None)`

디렉토리 *a*와 *b*를 비교하는 객체를 생성합니다.

세번째 키워드 인자 *ignore*는 두 디렉토리를 비교할 때 무시할 파일을 의미합니다.

*ignore*의 기본값은  `filecmp.DEAFAULT_IGNORE`으로 캐시 파일 같이 자동으로 생성되는 파일들입니다.

네번째 키워드 인자 *hide*는 파일 비교후 반환하는 파일 **경로의 생략할 부분**을 의미합니다.

기본값은 `[os.curdir, os.pardir]`으로 현재 디렉토리, 상위 디렉토리입니다.

<br>

아래는 `dircmp` 클래스의 메서드와 인스턴스입니다.


`dircmp`의 속성은 `__getattr__()`을 호출함과 동시에 연산이 이루어집니다(지연 연산 - lazy computing).<br>

메서드가 아니라고 값이 미리 저장되어 있는 형태가 아닙니다.

<br>

### dircmp - method

* report() -> None

    디렉토리 *a*, *b* 비교한 것을 출력합니다.

* report_partial_closure() -> None

    디렉토리 *a*, *b* 내부의 하위 디렉토리를 귀납적으로 탐색하지 않고 비교합니다.

    즉, 디렉토리를 하나의 파일로 간주한 채로 비교합니다.

* report_full_closure() -> None

    디렉토리 *a*, *b* 내부의 하위 디렉토리를 귀납적으로 탐색 후 비교합니다. 

    즉, 디렉토리 내부의 디렉토리까지 귀납적으로 탐색하고 비교합니다.

<br>

### dircmp - attributes

* left : `str`

    디렉토리 이름인 첫번째 인자 *a*를 반환합니다 

* right : `str`

    디렉토리 이름인 두번째 인자 *b*를 반환합니다 
    
* left\_list : `list`

    세번째 인자 *hide*와 네번째 인자 *ignore*를 제외한 첫번째 디렉토리 인자 *a*의 파일과 서브 디렉토리가 있는 리스트를 반환합니다

* right\_list : `list`

    세번째 인자 *hide*와 네번째 인자 *ignore*를 제외한 두번째 디렉토리 인자 *b*의 파일과 서브 디렉토리가 있는 리스트를 반환합니다

* common : `list`

    *a*, *b* 디렉토리에 공통으로 있는 파일과 서브 디렉토리가 담긴 리스트를 반환합니다.

* left\_only : `list`

    *a* 디렉토리에만 있는 파일과 디렉토리를 반환합니다.

* right\_only : `list`

    *b* 디렉토리에만 있는 파일과 디렉토리를 반환합니다.    

* common\_dirs : `list`

    *a*, *b* 디렉토리에 공통으로 존재하는 디렉토리들을 반환합니다.

* common\_files : `list`

    *a*, *b* 디렉토리에 공통으로 존재하는 파일들을 반환합니다.
    
* common\_funny : `list`

    파일 비교 연산시(`shallow = True`)에 쓰이는 `os.stat()`이 권한 문제 또는 다른 문제로 인해서 에러를 던진 파일,
    같은 이름을 가지지만, 다른 타입(확장자)를 가지는 파일들을 반환합니다.
    
    ```python
    
    
    ```

* same\_files : `list`

    *a*, *b* 디렉토리에 공통으로 존재하는 파일들을 반환합니다.

* diff\_files : `list`

    *a*, *b* 디렉토리에 공통으로 존재하지 않는 파일들을 반환합니다.

* funny\_files : `dict`

    ```python
    
    ```

* subdirs