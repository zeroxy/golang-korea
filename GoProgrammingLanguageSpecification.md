<h1> The Go Programming Language Specification </h1>

버전: Version of Apr 22, 2011 <br>
원문: <a href='http://golang.org/doc/go_spec.html'>http://golang.org/doc/go_spec.html</a> <br>
번역: Jongmin Kim (<a href='http://twitter.com/golanger'>@golanger</a>) <br>
상태: 번역중<br>
<br>
<br>
<br>
<br>
<hr />
<h1>서론(Introduction)</h1>

Go는 시스템 프로그래밍을 염두에 두고 디자인된 범용언어다. Go는 strong typing과 가비지 컬렉션 기능이 있고 명시적으로 병행(concurrent) 프로그래밍을 지원한다. Go 프로그램들은 패키지들로 구성되고 이 패키지들의 특성들로 인해 의존성을 효과적으로 관리할 수 있게 된다. 실행 가능한 바이너리를 만들기 위해 전통적인 컴파일/링크 방식을 사용한다.<br>
<br>
문법은 간결하고 규칙적이며, 통합 개발 환경(IDE: Integrated Development Environments)같은 자동화된 툴로 쉽게 분석 가능하다.<br>
<br>
<font color='#224A9A'><b>※ 역주: strong typing</font></b><br><b>strong typing이라는 건 string과 int형 변수가 형변환 없이 문자열 합치기(concatenation) 되지 않는 것 등을 말한다.</b><br>
참고: <a href='http://en.wikipedia.org/wiki/Strong_typing'>http://en.wikipedia.org/wiki/Strong_typing</a>

<hr />
<h1>표기법(Notation)</h1>

이 문서에서 표기 구문은 확장 백커스 나우어 형식(EBNF: Extended Backus-Naur Form)을 사용하였다.<br>
<br>
<pre><code>Production  = production_name "=" Expression "." .<br>
Expression  = Alternative { "|" Alternative } .<br>
Alternative = Term { Term } .<br>
Term        = production_name | token [ "..." token ] | Group | Option | Repetition .<br>
Group       = "(" Expression ")" .<br>
Option      = "[" Expression "]" .<br>
Repetition  = "{" Expression "}" .<br>
</code></pre>

Production들은 용어(terms)들과 뒤따라 나오는 연산자들로 구성된 표현들(expressions)이다. 아래로 갈수록 우선순위가 높아진다.<br>
<br>
<pre><code>|   alternation  (둘 중 하나)<br>
()  grouping<br>
[]  option (0 또는 1번)<br>
{}  repetition (0번 이상)<br>
</code></pre>

소문자 production name들은 어휘 토큰들(lexical tokens)을 구별하기 위해 사용된다. Non-terminal들은 카멜케이스(<code>CamelCase</code>)로 되어 있다. 어휘 심볼들은 쌍따옴표 "" 또는 back qutoes `` 안에 들어간다.<br>
<br>
a ... b 이런 형식은 a부터 b까지의 문자 집합을 나타낸다.<br>
<br>
<br>
<font color='#224A9A'><b>※ 역주: EBNF는 프로그래밍 언어의 문법을 표현하는 코드</font></b><br><b><i>terminal symbol</i> 과 <i>non-terminal production rule</i> 들로 구성되어 있는데, <i>non-terminal production rule</i> 은 <i>terminal symbol</i> 들이 어떻게 적합한 순서로 조합되어 사용될 수 있는지를 제약한다. 예를 들어보자.</b>

1. <i>x</i> 는 <i>xa</i> 가 될 수 있다.<br>
2. <i>x</i> 는 <i>ax</i> 가 될 수 있다.<br>
<br>
이 때 <i>a</i> 는 <i>terminal symbol</i> 이다. 왜냐하면 이 <i>a</i> 가 다른 것으로 바뀌게 될 거라는 규칙이 없기 때문이다. (한마디로 a는 a 자체로 끝났다. terminal이다) 반대로 <i>x</i> 는 바뀔 수 있다는 규칙이 있기 때문에 <i>non-terminal</i> 이다. 그래서 위 말을 이 예제로 풀어보면 <i>non-terminal</i> 인 <i>x</i> 가 <i>non-terminal rule</i> 에 의해 <i>terminal symbol</i> 인 <i>a</i> 의 위치, 순서를 제약하고 있다.<br>
<br>
이것만 기억하자. 규칙이 있으면 <i>non-terminal</i>. 규칙이 없으면 <i>terminal symbol</i>.<br>
<br>
참고:<br>
<a href='http://en.wikipedia.org/wiki/Terminal_symbol'>http://en.wikipedia.org/wiki/Terminal_symbol</a><br>
<a href='http://en.wikipedia.org/wiki/Extended_Backus%E2%80%93Naur_Form'>http://en.wikipedia.org/wiki/Extended_Backus%E2%80%93Naur_Form</a>

<hr />
<h1>소스 코드 표현(Source code representation)</h1>

소스 코드는 <a href='http://en.wikipedia.org/wiki/UTF-8'>UTF-8</a>로 인코딩된 유니코드 텍스트이다. 이 텍스트는 규격화되어 있지 않다. 그래서 하나의 악센트 코드 포인트는 악센트와 글자가 합쳐져서 만들어진 문자와 구별된다; 이런 문자는 두 개의 코드 포인트로 취급한다. 단순화하기 위해 이 문서에서는 <i>character</i> 라는 용어를 하나의 유니코드 포인트를 가리키는 말로 사용할 것이다.<br>
<br>
각 코드 포인트는 다른 문자를 나타낸다; 예로, 대문자와 소문자는 각각 다른 문자들인 것이다.<br>
<br>
구현 제약: 다른 툴들과의 호환성을 위해, 어떤 컴파일러는 NUL 문자(U+0000)를 프로그램 소스 내에서 허용하지 않을지도 모른다.<br>
<br>
<br>
<font color='#224A9A'><b>※ 역주: a와 à는 서로 다른 문자다.</font></b><br><b>à는 U+0061 (a), U+0300 (grave accent) 이 두 개의 코드 포인트가 인코딩된 문자다. 그래서 정규표현식에서 이런 문자의 매칭에 대한 이슈가 좀 있다. 이 문서에서는 간단하게 그냥 문자(혹은 character)라고 하면 하나의 유니코드 포인트를 말한다라고 가정한다.</b>

<h2>문자(Characters)</h2>
다음 용어들은 특정 유니코드 문자 분류를 나타내는데 사용된다.<br>
<br>
<pre><code>unicode_char   = /* 하나의 특수한 유니코드 코드 포인트 */ .<br>
unicode_letter = /* "Letter"로 분류된 유니코드 코드 포인트 */ .<br>
unicode_digit  = /* "Decimal Digit"로 분류된 유니코드 코드 포인트 */ .<br>
</code></pre>

<a href='http://www.unicode.org/versions/Unicode6.0.0/'>The Unicode Standard 6.0</a>의 섹션 4.5 "General Gategory"에서는 문자 카테고리의 집합을 정의하고 있다. Go는 저 "General Category" 항목에 있는 Lu, Ll, Lt, Lm, Lo를 유니코드 글자들로(unicode_letter), Nd는 유니코드 숫자들로(unicode_digit) 취급한다.<br>
<br>
<font color='#224A9A'><b>※ 역주:</font></b><br><b>Lu : Letter, uppercase</b><br>
Ll : Letter, lowercase <br>
Lt : Letter, titlecase <br>
Lm : Letter, modifier <br>
Lo : Letter, other <br>
Nd : Number, decimal digit<br>
<br>
<h2>글자와 숫자(Letters and digits)</h2>

밑줄 표시(underscore) 문자 <code>_</code> (U+005F)는 하나의 글자로 간주된다.<br>
<br>
<pre><code>letter        = unicode_letter | "_" .<br>
decimal_digit = "0" ... "9" .<br>
octal_digit   = "0" ... "7" .<br>
hex_digit     = "0" ... "9" | "A" ... "F" | "a" ... "f" .<br>
</code></pre>

<hr />
<h1>어휘 요소(Lexical elements)</h1>

<h2>주석(Comments)</h2>

두 가지 주석 표현 형식이 있다.<br>
<ol><li>라인 주석: <b>//</b> 으로 시작하는 것. 라인 주석은 개행과 같은 역할을 한다.<br>
</li><li>전면적인 주석: <b>/<code>*</code></b> 으로 시작해서 <b><code>*</code>/</b> 으로 끝나는 것. 여러 라인들이 개행과 같은 역할을 하거나 스페이스 역할을 한다.</li></ol>

주석은 중첩될 수 없다.<br>
<br>
<h2>토큰(Tokens)</h2>

토큰은 Go 언어의 표현법을 만들어낸다. 토큰에는 네 가지 분류가 있다: <b>식별자(indentifers), 키워드(keywords), 연산자와 구분자(operators and delimiters), 리터럴(literals)</b>. 스페이스들로 된 화이트 스페이스 (U+0020), 수평 탭(U+0009), 캐리지 리턴 (U+000D), 개행 (U+000A) 은 하나의 토큰으로 합쳐져서는 안되는 각각의 토큰들을 분리해주기 위한 용도가 아니면 그냥 무시된다. 또, 개행이나 파일의 끝은 하나의 세미콜론을 저절로 넣게 될 수 있다. 토큰을 분리하는 동안, 다음 토큰은 유효한 토큰을 형성하는 가장 긴 문자들의 시퀀스이다.<br>
<br>
<h2>세미콜론(Semicolons)</h2>

프로그래밍 언어의 문법에서는 의례히 세미콜론(;)을 문장을 끝내는 의미로 사용했다. 하지만 Go 프로그램에서는 다음의 두 가지 규칙에 의해 세미콜론을 생략할 수 있다.<br>
<br>
<ol><li>소스 코드를 토큰들로 나눌 때, 아래의 네 가지 경우에 있어 세미콜론은 자동으로 그 토큰들 사이에 들어간다. 라인의 마지막 토큰이<br>
<ul><li>식별자 (identifier)<br>
</li><li>정수(integer), 부동소수점(floating-point), 허수(imaginary), 문자(character), 또는 스트링<br>
</li><li>break, continue, fallthrough, return 키워드 중 하나<br>
</li><li>++, --, ), ], } 연산자와 구분자 중 하나<br>
</li></ul></li><li>한 라인에 복잡한 문장을 적을 수 있도록, 한 세미콜론은 ")"나 "}" 전에 생략될 수 있다.</li></ol>

관용적인 사용법을 보여주기 위해, 이 문서에 있는 예제 코드들에서는 위 규칙들을 이용해서 세미콜론을 생략하였다.<br>
<br>
<h2>Identifiers</h2>
식별자(identifier)는 변수나 타입같은 것들의 이름을 지어주는 것이다. 하나의 식별자는 하나 이상의 문자와 숫자들로 이뤄져 있다. 식별자의 첫번째 문자는 반드시 숫자가 아닌 문자가 와야 한다.<br>
<pre><code><br>
identifier = letter { letter | unicode_digit } .<br>
</code></pre>

<pre><code>a<br>
_x9<br>
ThisVariableIsExported<br>
αβ<br>
</code></pre>

몇몇 식별자는 미리 선언되어 있다.<br>
<br>
<h2>Keywords</h2>
아래 키워드들은 예약되어 있고 식별자로 사용될 수 없다.<br>
<pre><code>break        default      func         interface    select<br>
case         defer        go           map          struct<br>
chan         else         goto         package      switch<br>
const        fallthrough  if           range        type<br>
continue     for          import       return       var<br>
</code></pre>

<h2>Operators and Delimiters</h2>
아래 나오는 문자들은 연산자, 구분자, 특별한 토큰을 나타낸다.<br>
<pre><code>+    &amp;     +=    &amp;=     &amp;&amp;    ==    !=    (    )<br>
-    |     -=    |=     ||    &lt;     &lt;=    [    ]<br>
*    ^     *=    ^=     &lt;-    &gt;     &gt;=    {    }<br>
/    &lt;&lt;    /=    &lt;&lt;=    ++    =     :=    ,    ;<br>
%    &gt;&gt;    %=    &gt;&gt;=    --    !     ...   .    :<br>
     &amp;^          &amp;^=<br>
</code></pre>

<h2>Integer literals</h2>
integer literal은 정수 상수를 표현하는 일련의 숫자들이다. 십진법 표현이 아닌 방법으로 8진수, 16진수를 나타낼 수 있다. 8진수는 첫 글자로 0을 쓰고, 16진수는 0x 또는 0X를 쓴다.<br>
<pre><code>int_lit     = decimal_lit | octal_lit | hex_lit .<br>
decimal_lit = ( "1" … "9" ) { decimal_digit } .<br>
octal_lit   = "0" { octal_digit } .<br>
hex_lit     = "0" ( "x" | "X" ) hex_digit { hex_digit } .<br>
</code></pre>
<pre><code>42<br>
0600<br>
0xBadFace<br>
170141183460469231731687303715884105727<br>
</code></pre>

<h2>Floating-point literals</h2>
floating-point literal은 부동소수점 상수의 십진법 표현이다. 이것은 정수부, 소수점, 소수부, 지수부로 되어있다. 정수부와 소수부는 십진법 숫자로 되어 있다; 지수부는 e 또는 E로 표기하고 선택적으로 뒤에 부호있는 십진법 표기의 지수를 사용한다. 정수부나 소수부 중 하나는 표기가 생략될 수 있다; 소수점이나 지수부 중 하나는 생략될 수 있다.<br>
<pre><code>float_lit = decimals "." [ decimals ] [ exponent ] |<br>
            decimals exponent |<br>
            "." decimals [ exponent ] .<br>
decimals  = decimal_digit { decimal_digit } .<br>
exponent  = ( "e" | "E" ) [ "+" | "-" ] decimals .<br>
</code></pre>
<pre><code>0.<br>
72.40<br>
072.40  // == 72.40<br>
2.71828<br>
1.e+0<br>
6.67428e-11<br>
1E6<br>
.25<br>
.12345E+5<br>
</code></pre>

<h2>Imaginary literals</h2>
imaginary literal은 복소수 상수 중 허수부의 십진법 표기이다. 이것은 부동소수점 리터럴 또는 십진법 정수 표기 다음에 소문자 i로 구성되어 있다.<br>
<pre><code>imaginary_lit = (decimals | float_lit) "i" .<br>
</code></pre>
<pre><code>0i<br>
011i  // == 11i<br>
0.i<br>
2.71828i<br>
1.e+0i<br>
6.67428e-11i<br>
1E6i<br>
.25i<br>
.12345E+5i<br>
</code></pre>

<h2>Character literals</h2>
<h2>String literals</h2>






<br><br>