# textwrap - 읽기 좋은 문자열을 쉽게 만들어주는 모듈

textwrap 모듈은 런타임(run-time) 중 보여주는 문서를 더 편리하게 만들어주는 모듈입니다.

'***긴 문장을 길이에 따라서 줄바꿈('\n')을 입력하고, 문장 옆에는 '>'로 채워서 시각적으로 잘보이게 하고...***'

이런 노가다 작업을 해소해주는 노동집약적 모듈입니다.

## 목차

* textwrap 클래스의 함수(정적메서드)
    
    * wrap      - **문단을 일정 길이만큼 나누고싶을 때**
    
    * fill      - **문단을 일정 길이만큼 '\n'으로 나누고 싶을때**
    
    * shorten   - **문장을 일정 길이로 축약하고 싶을 때**
    
    * dedent    - **문장의 인덴트를 없앨 때**
    
    * indent    - **문단의 각 문장에 인덴트를 추가할 때**

* textwrap.Textwrapper 클래스

    * width
    
    * expand_tabs
    
    * replace_whitespace
    
    * drop_whitespace
    
    * initial_indent
    
    * subsequent_indent
    
    * break_long_words
    
    * break_on_hyphens

    * max_lines
    
    * placeholder
    
    * wrap
    
    * fill