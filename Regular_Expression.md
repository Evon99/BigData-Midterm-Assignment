# 정규 표현식

#### 1. 정의: 문자열을 처리하는 방법 중의 하나로 특정한 조건의 문자를 '검색'하거나 '치환'하는 과정을 매우 간편하게 처리 할 수 있도록 하는 수단

#### 2. 메타문자: 문자를 설명하기 위한 문자로, 문자의 구성을 설명하기 위해 원래 그 문자가 가진 뜻이 아닌 특별한 용도로 사용하는 문자<br>
정규 표현식에서 사용하는 메타문자: . ^ $ * + ? {} [] \ | ()

#### 3. 정규표현식의 종류 <br>
+ []: 문자 클래스인 []는 "[] 사이의 문자들과 매치"라는 의미를 가지며, []사이에는 어떤 문자도 들어갈 수 있다. <br>
\[a, b, c]: "a, b, c 중 한 개의 문자와 매치" <br>
ex) \[a, b, c]와 "a", "bus", "replit"와 어떻게 매치되는가?
  - "a": 정규식과 일치하는 문자인 a가 있으므로 매치
  - "bus": 정규식과 일치하는 문자인 b가 있으므로 매치
  - "replit": 정규식과 일치하는 문자인 a, b, c중 어느 하나도 포함하고 있지 않으므로 매치되지 않음
  
+ 하이픈(-), 캐럿(^): 하이픈(-)은 두 문자 사이의 범위(from - to)를 의미, 캐럿(^)은 반대를 의미 <br>
ex)
  - \[a-zA-Z]: a부터 z까지 그리고 A부터 Z까지로 알파벳 모두 매치
  - \[0-9]: 0부터 9까지의 범위로 숫자 매치
  - \[^0-9]: 0부터 9까지의 범위의 부정으로 숫자가 아닌 문자만 매치
    * []안에서는 부정의 의미로 사용
    * []가 없으면 문자열의 처음을 뜻함(끝은 $로 표시)

+ \역슬래쉬: \[0-9]또는 \[a-zA-Z]와 같은 정규표현식은 \역슬래쉬를 이용해 간단하게 표현할 수 있다.
  - \d: 숫자와 매치, \[0-9]와 동일한 표현식
  - \D: 숫자가 아닌 것과 매치, \[^0-9]와 동일한 표현식
  - \s: whitespace 문자와 매치, \[ \t\n\r\f\v]와 동일한 표현식이다. 맨 앞의 빈 칸은 공백문자(space)를 의미
  - \S: whitespace 문자가 아닌 것과 매치, \[^\t\n\r\f\v]와 동일한 표현식
  - \w: 문자+숫자(alphanumeric)와 매치, \[a-zA-Z0-9_]와 동일한 표현식
  - \W: 문자+숫자(alphanumeric)가 아닌 문자와 매치, \[^a-zA-Z0-9_]와 동일한 표현식
  - \b: 단어 경계 (`\w`와 `\W`의 경계)
  - \B: 비단어 경계
대문자로 사용된 것은 소문자의 반대임
정규식 상의 특별한 의미가 있는 문자들을 문자 그대로 쓸 때 앞에 붙혀 사용

+ Dot(.): 줄바꿈 문자인 \n을 제외한 모든 문자와 매치됨을 의미
ex)
  - a.b: "a + 모든문자 + b"와 같고 와b라는 문자 사이에 어떤 문자가 들어가도 모두 매치가 된다는 의미
      * "aab": 가운데 문자 a가 모든 문자를 의미하는 .과 일치하므로 정규식과 매치
      * "a0b": 동일하게 가운데 문자 0이 모든 문자를 의미하는 .과 일치하므로 정규식과 매치
      * "abc": a와 b사이에 어떤 문자가 존재하지 않기 때문에 위 정규식과 매치되지 않음
      * "a[.]b": []는 내부에 메타문자가 들어가더라도 문자 그대로 인식해주는 특징을 갖고 있습니다. 따라서 a.b와 매치되고 a0b와 같은 문자열은 매치되지 않음

+ 반복 관련 메타 문자 **\* + ? {}**
  - 반복(\*): 메타문자 \*은 \* 바로 앞에 있는 문자가 0부터 무한대로 반복 될 수 있다는 의미 <br>
  ou*t: o + u(0번 이상 반복) + t 를 의미 -> ot, ott, ouuuuuut 모두 매치
  - 반복(+): 반복을 나타내는 또 다른 문자인 +, +는 *과 달리 최소 1번 이상 반복될 때 사용 <br>
  ou\*t -> ot, out, ouuut중 ot는 매치되지 않음
  - 반복({m, n}): {}는 원하는 반복 횟수를 지정할 때 사용, m에서 n까지 반복, m이상인 경우, n이하인 경우 등 자유롭게 원하는 만큼 조절 가능
      * {m}: 반드시 m번 반복
        + ou{2}t: "o + u(반드시 2번 반복) + t"
      * {m, n}: m~n회 반복
        + ou{2, 5}t: "o + u(2~5회 반복) + t"
      * {m,}, {,n}: m회 이상 반복, n회 이하 반복
  - 반복(?): ?는 반복은 아니지만 비슷한 개념으로 {0, 1}과 같은 의미를 지님. 즉, 문자가 있거나 없거나 둘 다 매치 되는 경우 뜻함
      * ab?c: "a + b(있어도 되고 없어도 된다) + c"
*, +, ?메타 문자는 모두 {m, n} 형태로 고쳐 쓰는 것이 가능하지만 가독성을 위해 *, +, ?메타 문자를 사용하는 것이 좋다.
+ 괄호(): 그룹을 나타내며, 그룹화된 패턴을 처리할 때 사용
  - (ab)+: ab가 1번 이상 반복(+)되는 패턴을 의미
    * ab: ab가 1번 반복으로 정규식과 매치됨
    * abab: abab가 2번 반복으로 정규식과 매치됨
    * ababab: ababab가 3번 반복으로 정규식과 매치됨
    * abc: ab가 반복되지 않으므로 정규식과 매치되지 않음
    * aab: ab로 시작하지 않으므로 정규식과 매치되지 않음
+ 파이프(|): 둘 중 하나의 패턴과 일치를 의미
  - a|b: a 또는 b와 일치를 의미
#### 4. 정규표현식의 예시 (이메일)
+ \w+@\w+\\.\[a-zA-Z]{2,3}
  - \w+: 1개 이상의 알파벳 대소문자, 숫자, 밑줄 (_) 문자를 나타냅니다. 이메일 주소의 로컬 파트인 이메일 아이디 부분에 해당
  - @: 이메일 주소에서 "@" 기호를 나타냄.
  - \w+: 1개 이상의 알파벳 대소문자, 숫자, 밑줄 (_) 문자를 나타냅니다. 이메일 주소의 도메인 부분에 해당
  - \\.: 마침표 (.) 문자를 나타냅니다. 이메일 주소의 도메인과 최상위 도메인 사이에 위치
  - \[a-zA-Z]{2,3}: 2자리 또는 3자리의 알파벳 대소문자를 나타내며 이메일 주소의 최상위 도메인에 해당 <br>
  ex) "com", "net", "edu" 등

#### 5. 정규표현식의 필요 이유
+ 문자열 패턴 탐색: 정규표현식은 특정 패턴을 가진 문자열을 찾는 데 사용된다. 예를 들어, 이메일 주소, 전화번호 등과 같은 특정 형식의 문자열을 찾을 때 유용함. 이를 통해 데이터에서 원하는 정보를 추출하거나 유효성을 검사할 수 있음.
+ 데이터 유효성 검증: 정규표현식은 입력된 데이터가 지정된 형식에 맞는지 확인하는 데 사용된다. 예를 들어, 사용자가 입력한 이메일 주소가 올바른 형식인지, 비밀번호가 요구 조건을 충족하는지 등을 검사할 수 있음.
+ 문자열 처리 및 변환: 정규표현식은 문자열을 추출하거나 대체, 변환하는 데 유용함. 예를 들어, 특정 패턴을 가진 문자열을 추출하여 다른 형식으로 변환하거나, 공백을 제거하거나, 특정 문자를 다른 문자로 대체하는 등의 작업을 수행할 수 있음.
+ 텍스트 분석과 데이터 추출: 정규표현식을 사용하면 텍스트 데이터에서 원하는 정보를 추출할 수 있다. 예를 들어, 로그 파일에서 특정 이벤트의 발생 횟수를 세거나, 웹 스크래핑을 통해 웹 페이지에서 필요한 데이터를 추출할 때 정규표현식을 활용할 수 있음.
+ 프로그래밍 언어와 도구 지원: 많은 프로그래밍 언어와 텍스트 편집기에서는 정규표현식을 지원하며, 문자열 처리와 관련된 다양한 작업에 사용할 수 있는 함수와 도구를 제공한다. 따라서 정규표현식을 이해하고 사용하면 문자열 작업을 효율적으로 수행할 수 있음.
-> 정규표현식은 문자열 처리의 필수 도구로, 데이터 유효성 검사, 패턴 매칭, 문자열 추출 및 변환 등 다양한 작업에 활용된다. 이를 통해 코드의 효율성을 높일 수 있다.

#### 6. 파이썬에서의 정규표현식 <br>
파이썬은 정규표현식을 지원하기위해 re(regular expression) 모듈을 제공 <br>
Ex) 
```
import re
pattern = re.compile("정규식")  
```
re.compile을 사용하여 정규 표현식을 컴파일. re.compile의 결과인 객체 pattern에 입력한 정규식의 대한 정보가 담겨 있음

```
import re
p = re.compile('[a-z]+')
```
* 예시에 사용될 객체 p 생성
컴파일된 패턴 객체는 다음의 4가지 메소드를 제공
+ match(): 문자열의 처음부터 정규식과 매치되는지 조사, 반환값: match 객체
  - \>>> m = p.match("bigdata") <br>
    \>>> print(m) <br>
    <re.Match object; span=(0, 7), match='bigdata'> <br>
    "python"은 \[a-z]+ 정규식에 부합되므로 match 객체를 돌려줌 <br>
    in Python, 매치 여부 확인법
    ```
    p = re.compile(정규표현식)
    m = p.match(문자열)

    if m:
      print("정규식 일치")
    else:
      print("정규식 불일치")
    ```
  - 4가지 메서드
    * group(): 매치된 문자열을 돌려줌
    * start(): 매치된 문자열의 시작 위치를 돌려줌
    * end(): 매치된 문자열의 끝 위치를 돌려줌
    * span(): 매치된 문자열의 (시작, 끝)에 해당하는 튜플을 돌려줌
    * Ex) <br>
    \>>> m = p.match("bigdata") <br>
    \>>> m.group() <br>
    'bigdata' <br>
    \>>> m.start() <br>
    0 <br>
    \>>> m.end() <br>
    7 <br>
    \>>> m.span() <br>
    (0, 7) <br>
    search()의 경우
    \>>> m = p.search("50 bigdata") <br>
    \>>> m.group() <br>
    'bigdata' <br>
    \>>> m.start() <br>
    3 <br>
    \>>> m.end() <br>
    10 <br>
    \>>> m.span() <br>
    (3, 10)

    * 모듈 단위의 수행법 <br>
    m = re.match('\[a-z]+', "bigdata")
+ search(): 문자열 전체를 검색하여 정규식과 매치되는지 조사
  - \>>> m = p.search("bigdata") <br>
    \>>> print(m) <br>
    <re.Match object; span=(0, 7), match='bigdata'>> <br>
    3 python 문자열은 첫번째 문자가 숫자이므로 match 메서드에서는 None을 반환함. 하지만 search 메서드는 문자열의 처음부터 검색하는 것이 아니라 문자열 전체를 검색하기 때문에 "python" 문자열과 매치돼서 match객체를 반환
    
+ findall(): 정규식과 매치되는 모든 문자열(substring)을 리스트로 돌려줌
  - \>>> result = p.findall("life is too short") <br>
    \>>> print(result) <br>
    \['life', 'is', 'too', 'short'] <br>
    정규식과 일치하는 부분인 각 단어들이 반환되는 것이 확인됨
+ finditer(): 정규식과 매치되는 모든 문자열(substring)을 반복 가능한 객체로 돌려줌
  - finditer는 findall과 동일하지만 그 결과로 반복 가능한 객체를 돌려줌 <br>
    \>>> result = p.finditer("life is too short") <br>
    \>>> print(result) <br>
    <callable_iterator object at 0x7f9b68af4220> <br>
    \>>> for r in result: print(r) <br>
    ... <br>
    <re.Match object; span=(0, 4), match='life'> <br>
    <re.Match object; span=(5, 7), match='is'> <br>
    <re.Match object; span=(8, 11), match='too'> <br>
    <re.Match object; span=(12, 17), match='short'> <br>

#### 7. 정규표현식의 컴파일 옵션
컴파일 옵션 사용 시 re.DOTALL처럼 전체 옵션이름을 써도 되고, re.S처럼 약어 사용 가능
+ DOTALL(S): 메타 문자 Dot(.)이 줄바꿈 문자를 포함하여 모든 문자와 매치될 수 있도록 한다
  - Ex) <br>
  \>>> p = re.compile('a.b', re.DOTALL)
  \>>> m = p.match('a\nb')
  \>>> print(m)
  <_sre.SRE_Match object at 0x01FCF3D8>
+ IGNORECASE(I): 대소문자에 관계없이 매치할 수 있도록 한다.
  - Ex) <br>
  \>>> p = re.compile('\[a-z]', re.I) <br>
  \>>> p.match('bigdata') <br>
  <re.Match object; span=(0, 1), match='b'> <br>
  \>>> p.match('Bigdata') <br>
  <re.Match object; span=(0, 1), match='B'> <br>
  \>>> p.match('BIGDATA') <br>
  <re.Match object; span=(0, 1), match='B'> <br>
  \[a-z] 정규식은 소문자만을 의미하지만 re.I옵션으로 대소문자 구별없이 매치되는 것을 볼 수 있음
+ MULTILINE(M): 여러줄과 매치할 수 있도록 한다. (^, $ 메타문자의 사용과 관계가 있는 옵션)
  - <pre>
    <code>
    import re
    p = re.compile("^bigdata\s\w+")

    test = """bigdata one
    Today is Sunday
    bigdata two
    This is Example
    bigdata three"""

    print(p.findall(test))
    </code>
    </pre>
    <pre>
    <code>
    ['bigdata one']
    </code>
    </pre>
    "^bigdata\s\w+": 문자열의 시작이 "bigdata"로 시작하고, 공백이 있으며, 이어서 하나 이상의 단어 문자가 있는 패턴을 의미 <br>
    ^메타 문자에 의해 bigdata이라는 문자열을 사용한 첫 번째 줄만 매치된 것을 알 수 있음 <br>

  하지만 ^메타 문자를 문자열 전체의 처음이 아니라 각 라인의 처음으로 인식시키고 싶을 때 사용하는 옵션은 re.MULTILINE <br>
  Ex)
    <pre>
    <code>
    import re
    p = re.compile("^bigdata\s\w+", re.MULTILINE)

    test = """bigdata one
    Today is Sunday
    bigdata two
    This is Example
    bigdata three"""

    print(p.findall(test))
    </code>
    </pre>
    <pre>
    <code>
    ['bigdata one', 'bigdata two', 'bigdata three']
    </code>
    </pre>
    메타 문자 ^가 각 줄마다 적용되는 것이 확인됨
+ VERBOSE(X) - verbose 모드를 사용할 수 있도록 한다. (가독성이 안좋은 정규식을 보기 편하게 만들수 있고 주석 등을 사용할 수 있게된다.)
  - Ex)
  <pre>
  <code>
  charref = re.compile(r'&[#](0[0-7]+|[0-9]+|x[0-9a-fA-F]+);')
  </code>
  </pre>
  이 코드의 정규식은 가독성이 안좋아 의미를 해석하기 어려워 보인다. 하지만 VERBOSE를 쓰면 다음과 같이 쓸 수 있다.
  <pre>
  <code>
  charref = re.compile(r'''
  &[#]                # "&#"로 시작하는 문자열과 매치. 이는 숫자 기반의 문자 엔티티 참조를 나타냅니다.
  (
     0[0-7]+         """
    0: 숫자 0을 나타냅니다. 8진수는 항상 0으로 시작합니다.
    [0-7]: 0부터 7까지의 숫자 중 하나와 일치합니다. 8진수에서는 유효한 숫자 범위가 0부터 7임
    +: 바로 앞의 패턴(여기서는 [0-7])이 하나 이상의 반복을 나타냅니다. 따라서, 0을 제외한 다른 숫자가 한 번 이상 반복되어야 합니다.
    """
   | [0-9]+          """
    | : "또는"을 의미
    [0-9]: 0부터 9까지의 숫자 중 하나와 일치합니다. 이는 10진수의 모든 숫자를 포함
    +: 바로 앞의 패턴(여기서는 [0-9])이 하나 이상의 반복을 나타냅니다. 따라서, 숫자가 한 번 이상 반복
    """
   | x[0-9a-fA-F]+   """
    | : "또는"을 의미
    x: 문자 "x"와 정확히 일치해야함
    [0-9a-fA-F]: 0부터 9까지의 숫자 또는 a부터 f까지의 대소문자 알파벳 중 하나와 일치합니다. 이는 16진수의 유효한 숫자와 알파벳을 포함
    +: 바로 앞의 패턴(여기서는 [0-9a-fA-F])이 한 번 이상의 반복을 나타냄. 따라서, 숫자 또는 알파벳이 한 번 이상 반복되어야 함
    """
  )
  ;                   # 세미콜론으로 끝나는 문자열과 매치함. 이는 문자 엔티티 참조의 끝을 나타냄.
  ''', re.VERBOSE)
  </code>
  </pre>
  re.VERBOSE 옵션은 문자열에 사용된 whitespace가 컴파일 시 제거되며, #을 이용해 주석문을 달 수 있음

#### 8. 정규표현식의 캡처
+ 캡처: 원하는 부분만을 추출하고 싶을 때 사용하는 것
  - Ex) 전화번호에서 중간 자리만 추출 하고 싶을 때
  <pre>
  <code>
  import re
    
  print(re.findall('\d{3}-(\d{4})-\d{4}', '010-1152-4123'))
  </code>
  </pre> 
  결과
  <pre>
  <code>
  ['1152']
  </code>
  </pre>
  추출하고 싶은 부분을 괄호()로 감싸주면 된다.
+ groups(): match객체에서 일치되는 문자열을 반환하는 group()과 달리 캡처()로 감싼 부분을 반환함
  - Ex) <br>
    \>>> m = re.search('\d{3}-(\d{4})-(\d{4})', '010-1152-4123') <br>
    \>>> print(m) <br>
    <re.Match object; span=(0, 13), match='010-1152-4123'> <br>
    \>>> print(m.group()) <br>
    010-1152-4123 <br>
    \>>> print(m.groups()) <br>
    ('1152', '4123') <br>
    \>>> m.group(1) <br>
    '1152' <br>
    \>>> m.group(2) <br>
    '4123' <br>
    \>>> m.groups() <br>
    ('1152', '4123') <br>
+ 비 캡처 그룹: 그룹화를 하려면 ()를 써야하는데, 캡처를 하고싶지 않을 땐 (?:\<regex>)를 사용한다
  - Ex) <br>
  \>>> print(re.findall('(456)+','456456')) <br>
  \['456'] <br>
  변경된 코드 <br>
  \>>> print(re.findall('(?:456)+','456456')) <br>
  \['456456'] <br>

#### 9. 정규표현식의 치환
+ 파이썬의 replace 메서드는 정규식 패턴에 대응하는 문자열을 찾지 못함 -> re.sub 메서드를 사용
  - re.sub 메서드의 파라미터
    * pattern: 매치시킬 패턴
    * repl: 변경할 문자
    * string: 적용 문자열
    * count: 치환개수(최대값)
  - Ex) <br>
    \>>> print(re.sub(pattern='Bigdata', repl='Java', count=2, \\ <br>
    ...              string='Bigdata, Bigdata, C++ are computer language.')) <br>
    Java, Java, C++ are computer language.
  - re.subn 메서드: re.sub 기능에 치환된 개수의 튜플을 반환
    \>>> print(re.subn(pattern='Bigdata', repl='Java', count=2, \\ <br>
    ...              string='Bigdata, Bigdata, C++ are computer language.')) <br>
    ('Java, Java, C++ are computer language.', 2) <br>

#### 10. 정규표현식의 split
+ re.split: 파이썬의 문자열 메서드 중 split와 유사
  - Ex) <br>
    \>>> print(re.split('<\[^<>]*>', <br>
               '\<html> Wow \<head> header \</head> \<body> Hey \</body> \</html>')) <br>
    \['', ' Wow ', ' header ', ' ', ' Hey ', ' ', ''] <br>
    * <: '<'를 나타냄 
    * \[^<>]: <와 >를 제외한 모든 문자가 0번 이상 반복되는 부분을 나타냄. 이는 <와 > 사이에 있는 내용을 의미
    * \>: > 문자를 나타냄

#### 11. 정규표현식의 연산을 섞은 치환
+ re.sub를 쓸 때 일치부에 나타나지도 않고, literal text에도 나타나지 않는 문자열로 치환하고 싶은 경우가 생길 수 있음 <br>
  re.sub는 인자 repl을 받을 때 함수로도 받을 수 있다. 함수는 인자로 match 객체를 받으며 문자열을 반환해야함
  - Ex) 소수로 표현된 숫자를 찾은 다음 퍼센티지로 변환하기
    <pre>
    <code>
    def convert_percentage(match_obj):
    number = float(match_obj.group())
    return str(number * 100) + '%'

    print(re.sub(pattern=r'\b0\.\d+\b',
             repl=convert_percentage,
             string='Red 0.250, Green 0.001, Blue 0.749, Black 1.5'))
    </code>
    </pre>
    결과
    <pre>
    <code>
    Red 25.0%, Green 0.1%, Blue 74.9%, Black 1.5
    </code>
    </pre>

#### 12. 정규표현식의 조건문
+ 조건문은 캡처 그룹을 사용해 앞에서 문자열이 일치되었는지 아닌지에 따라 다음 부분에서는 다른 일치 조건을 제시할 때 쓰임 <br>
**(?(숫자)맞으면|아니면)**
+ Ex) <br>
(a)?b(?(1)c|d) == abc|bd
  1. 먼저 a를 검사. 만약 찾는 문자열에 ‘a’가 있으면 첫 번째 명시적 캡처에는 ‘a’가 들어가고, ‘a’가 없으면, 빈 문자열이 (1)에 저장
  2. 다음으로 b. 만약 ‘b’가 없으면 정규식은 일치하지 않습니다. ‘b’가 있다고 가정
  3. (?(1)c|d) 이 조건문은 (1)에 값이 존재하면 c, 존재하지 않으면 d를 의미. 즉 a가 존재했었다면 abc가 완성되고, a가 존재하지 않았다면 bd가 완성
  