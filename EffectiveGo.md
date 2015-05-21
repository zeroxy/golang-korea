# 요약 #
  * 내용 : Go언어를 잘 사용하는 방법 소개
  * 원문 : [Effective Go](http://golang.org/doc/effective_go.html)
  * 번역자 : jake.jeongsik.mun@gmail.com, surinkim10@gmail.com




# Go언어를 잘 사용하는 방법 #

---

## 소개 ##
비록 기존의 language로부터 아이디어를 빌려오긴 했지만 Go는 새로운 언어입니다. Go 프로그램들은 다른 언어로 만들어진 프로그램들과는 특징이 다르며, Go언어는 이런 Go 프로그램들을 효율적으로 만들어주는 독특한 속성을 가지고 있습니다. C++ 또는 Java 프로그램을 Go 프로그램으로 바로 변환하는 것은 만족스럽지 않은 결과를 가져올 것입니다.-Java 프로그램은 Go가 아니라 Java로 작성되어있습니다. 반면, Go 언어의 상관관계를 생각해보면 성공적이지만 꽤 다른 프로그램들이 나오게 될 것입니다. 다시말해, Go 언어를 잘 쓰기 위해서, Go언어의 속성과 특징을 이해하는 것이 중요합니다. 또한, Go언어로 프로그래밍을 하기 위해서 naming, formatting, 프로그램 구조 등과 같은 기존 규약들을 아는 것이 중요합니다. 그렇게 작성된 프로그램들은 다른 Go언어 개발자들이 이해하는데 도움이 될 것입니다.
이 문서는 명확하고, 특징적인 Go code를 작성하기 위한 Tip들을 제공할 것이며 Go언어의 명세와 입문서(둘다 먼저 읽어봐야만 하는)를 보충해줄 것 입니다..

**[예시]**

Go package source들은 core library 뿐만아니라 Go 언어를 어떻게 사용하는지에 대한 예를 제공합니다. 만약, 어떻게 문제에 접근해야하는지 또는 어떻게 구현됐을지 궁금하다면, Go package source가 찾고있는 답이나 아이디어  또는 배경지식을 알려줄 수 있을 것입니다.

---

## 형식화 하기(Formatting) ##
formatting 문제는 논쟁이 많지만 중요한 문제입니다. 사람들은 서로 다른 formatting 형태에 적응할 수 있지만 만약 새로운 formatting 형태에 적응할 필요가 없고, 모두가 같은 formatting 형태를 지원해서 formatting에 시간 소모를 줄일 수 있다면 더 좋습니다. 이 문제는 formatting에 대한 규정집 같은 가이드 없이 어떻게 이상적인 상태에 접근하느냐 입니다.

Go 언어와 함께라면, 우리는 특별한 접근 방법을 가지고 대부분의 formatting 문제들을 다룰 수 있습니다. _**gofmt tool**_ 은 Go 프로그램을 읽고 들여쓰기나 수직 정렬, 필요하다면 주석을 개편하는 것과 같은 표준 형태의 source를 출력해줍니다. 만약, 새로운 형태의 layout을 어떻게 다뤄야 하는지 알기를 원한다면, _**gofmt**_ 을 실행 시켜보십시요. 만약, 결과가 좋아 보이지 않는다면, 프로그램을 재정렬하십시요(또는 _**gofmt**_ 의 버그로 제출하십시요).

한 예로, 주석을 정렬하는데 시간을 낭비할 필요가 없습니다. _**gofmt**_ 이 당신을 위해 주석을 정렬해 줄 것 입니다.

다음과 같은 주어진 선언에서
```
   type T struct {
       name string // name of the object
       value int // its value
   }
```
아래와 같이 _**gofmt**_ 이 정렬 해줄 것 입니다.
```
   type T struct {
       name    string // name of the object
       value   int    // its value
   }
```



표준 package 안에 모든 Go code는 _**gofmt**_ 으로 formatting되어 있습니다.
formatting관련 몇가지를 자세히 살펴보면, 매우 간단합니다.

  * **들여쓰기(Indentation)**
> 들여쓰기를 위해서 tab을 사용합니다. _**gofmt**_ 는 tab을 default 들여쓰기로 변환해줄 것입니다. 꼭 그래야 할 경우만 space를 사용하세요.

  * **줄 길이(Line Length)**
> Go 언어는 줄 길이의 제한이 없습니다. 오버플로우에 대해서는 걱정할 필요가 없습니다. 만약 줄이 너무 길다면 줄을 tab으로 둘러싸면 됩니다.

  * **괄호(Parentheses)**
Go는 C나 Java보다 괄호를 더 적게 씁니다. if, for, switch 제어구조에서는 괄호가 필요하지 않습니다. 연산자 우선순위는 보다 짧고 명료합니다.
```
    x<<8 + y<<16
```
그래서 위 코드는 다른 언어들과 다르게 쉬프트와 더하기 연산 사이에 공백이 무엇을 먼저 계산해야 하는지 알기 쉽도록 해줍니다.
(역주: Go에서는 << 연산이 + 보다 우선이기 때문에 따로 괄호를 주지 않아도 된다. 하지만 C, C++, Java, Python에서는 + 연산이 쉬트트 연산보다 우선 순위가 높기 때문에 저 코드대로 실행하면 결과가 달라진다. 그래서 (x<<8) + (y<<16) 과 같이 괄호를 해줘야 위 Go에서의 코드와 결과가 같아진다.)

---

## 주석(Commentary) ##
Go 언어는 C언어 형태의 `/* */` 블록 주석 및 C++언어 형태의 // 라인 주석을 지원 합니다. 라인 주석들은 일반적이며 블록 주석은 대부분의 package 주석에 나타납니다. 또한, 대량의 code를 주석처리 하는데 유용합니다.

_**program-,web server-godoc**_ 은 Go source 파일들을 이용해 package의 내용을 문서화하는 작업을 합니다. 새로운 라인 없이 선언의 최상위 레벨에 나타나는 주석은 선언과 함께 현재 항목에 대한 설명을 제공하기 위해 발췌됩니다. 이런 주석의 형태는 _**godoc**_ 이 만들어 내는 문서의 질을 결정하게 됩니다.

모든 package는 반드시 package에 대한 주석을 가지고 있어야 하며 블록 주석은 package 항목 앞에 기술되어야 합니다. 여러개의 파일로 구성된 package의 경우 package 주석은 하나의 파일에만 있으면 됩니다. (여러개 중 어느 파일이든 상관없음). package 주석은 package를 소개하고 package 전체에 관련된 정보를 제공해야만 하며 godoc의 첫 페이지에 나타나게 될 것입니다.
반드시 제공 되어야만 하는 사항은 다음과 같습니다.
```
/*
    Package regexp implements a simple library for
    regular expressions.

    The syntax of the regular expressions accepted is:

    regexp:
        concatenation { '|' concatenation }
    concatenation:
        { closure }
    closure:
        term [ '*' | '+' | '?' ]
    term:
        '^'
        '$'
        '.'
        character
        '[' [ '^' ] character-ranges ']'
        '(' regexp ')'
*/
package regexp
```

만약 package가 단순하다면 package 주석을 만드는 것은 쉬울 것입니다.

```
// Package path implements utility routines for
// manipulating slash-separated filename paths.
```

주석은 `'*'` 들로 만들어진 banner 같은 추가적인 형식을 필요로 하지 않습니다. _**godoc**_ 의 일반적인 결과물은 고정폭의 font를 제공하지 않을 것이기 때문에 _**gofmt**_ 과 마찬가지로 _**godoc**_ 을 정렬하기 위해 space에 의존하지 마십시요. 주석은 해석되지 않은 평문이기 때문에 _this_ 같은 HTML이나 다른 형태의 주석들(annotation)은 글자 그대로 재생성될 것이므로 사용해서는 안됩니다.(역주:HTML등을 사용해도 글자 그대로 출력될 뿐 HTML과 같은 기능 동작을 하지 않는다는 의미임). 환경에 따라 _**godoc**_ 은 주석의 형태를 변경하지 못할 수도 있습니다. 따라서 주석이 확실히 보기 좋도록 만들어야만 합니다.:정확한 철자, 구두법, 문장구조를 사용하고 긴문장을 줄이십시요.

package 내부에서는 최상위 선언을 선행하는 어떤 주석도 그 선언을 설명하기 위한 문서 주석(doc comment)으로 제공되어서는 안됩니다. 프로그램 안에서 모든 export된(대문자) 이름은 문서 주석(doc comment)을 가지고 있어야만 합니다.

문서 주석(doc comment)은 자동화된 다양한 형태로 제공할 수 있는 완전한 문장일 때 가장 좋습니다. 첫번째 문장은 선언된 이름으로 시작되는 한문장의 요약이어야 합니다.

```
// Compile parses a regular expression and returns, if successful, a Regexp
// object that can be used to match against text.
func Compile(str string) (regexp *Regexp, error os.Error) {
```

Go 언어의 선언 방법은 선언들을 grouping하는 것을 가능하게 합니다. 하나의 문서 주석(doc comment)이 연관된 상수나 변수 그룹을 설명하는 것이 가능합니다만 전체 선언이 제시되기 때문에 그런 주석은 형식적일 수 있습니다.

```
// Error codes returned by failures to parse an expression.
var (
    ErrInternal      = os.NewError("internal error")
    ErrUnmatchedLpar = os.NewError("unmatched '('")
    ErrUnmatchedRpar = os.NewError("unmatched ')'")
    ...
)
```

이름이 private으로 선언되어 있다하더라도 grouping은 항목들 사이의 관계를 나타낼 수 있습니다.(예를 들면 아래와 같이 변수 집합이 mutex에 의해 보호되고 있다는 사실을 나타낼 수 있음)

```
var (
    countLock   sync.Mutex
    inputCount  uint32
    outputCount uint32
    errorCount  uint32
)
```


---

## 명칭(Names) ##
명칭들은 다른 언어들에서와 마찬가지로 Go 언어에서도 중요한 역할을 합니다. 몇몇의 경우 명칭은 의미에 영향을 줄 수 있습니다. 예를 들어 package 밖에서의 이름의 유효성은 첫번째 문자가 대문자로 시작되느냐 아니냐에 의해서 결정됩니다. 그러므로 Go 프로그램의 naming 규정을 확인할 필요가 있습니다.

### Package names ###
package가 import될 때, package 이름은 package 내용에 대한 접근자가 됩니다.

```
import "bytes"
```
위와 같이 썼다면 importing된 package는 byte에 관해 다룰 것입니다. 만약 모든 사람들이 같은 package를 참조하기 위해 같은 이름을 사용한다면 편리할 것이며, 그것은 곧 package의 이름이 반드시 좋아야 한다는 것을 의미합니다.: 짧고, 간결해야하며, 유추가능해야 함. 규정에 따라, package 이름은 **소문자를 사용하며, 한단어 이름** 을 사용합니다. 밑줄(`_`) 또는 대소문자를 섞어서 사용할 필요가 없습니다. 당신의 package를 사용하는 모든 사람이 그 이름을 사용할 것이기 때문에 간결함이 문제가 될 수도 있지만, package 이름은 import를 위한 기본 이름으로만 사용되기 때문에 중복 및 충돌에 대해서는 걱정할 필요가 없습니다. (모든 소스코드에 있어서 유일한 이름일 필요는 없으며, 가끔 충돌이 발생하는 경우에 한해서 국지적으로(local) 쓰기위해 다른 이름을 사용하면 됩니다.) import되는 경우 파일의 이름으로 어떤 package가 사용되어 졌는지를 결정할 것 이기 때문에 혼동되는 경우는 거의 없습니다.

또 다른 규정은 package 이름은 package 소스 디렉토리에 기반한 이름이어야 한다는 것입니다. 즉, package 소스 디렉토리가 **src/pkg/container/vector** 라고 가정하면 그 package는 "container/vector"로 import 되어지며 이름은 vector 입니다.(container\_vector 또는 containerVector가 아닙니다.)

package를 import하려면 package 내용을 참조하기 위해 package의 이름을 사용할 것입니다. `'.'` 기호는 test나 일반적이지 않은 상황을 위한 것이며 꼭 필요하지 않다면 피해야합니다. 예를 들어 bufio package에서 buffered reader type은 BufReader가 아니라 Reader로 불립니다. 왜냐하면 사용자들은 buffered reader type을 bufio.Reader로써 볼 것이고, 그것이 분명하고 간결한 이름이기 때문이며, 더욱이, import된 객체들은 항상 그 객체의 package 이름과 함께 사용되기 때문입니다. bufio.Reader는 io.Reader와 충돌을 일으키지 않습니다. 유사한 예로, ring.Ring(Go언어에서 한 생성자의 정의임)의 새로운 개체를 만들기 위한 함수는 일반적으로 NewRing으로 호출될 것 입니다. 그렇지만 Ring은 ring package에 의해 export된 유일한 type이며, 그 package는 ring이기 때문에 새로운 개체는 그냥 New라고 호출되며 그 package의 client들에게는 ring.New로 보일 것입니다. 좋은 이름을 선택하기 위해서는 package 구조를 사용하십시요.

또 다른 간단한 예는 once`.`Do 입니다. once`.`Do(setup)은 읽기에 쉽고 once`.`DoOrWaitUntilDone(setup) 보다 좋습니다. 긴 이름들은 읽기에 쉽지 않습니다. 만약에 그 이름이 뭔가 복잡하고 어려운 무언가를 대표한다면, 그 이름에 모든 정보를 넣기보다는 doc 주석으로 남기는 것이 더 좋습니다.
### Getters ###
Go 언어는 getter와 setter를 자동으로 제공하지 않습니다. 당신 스스로 getter와 setter를 만드는 것은 잘못된 것이 아니며, 그렇게 하는 것은 흔한 일입니다. 당신이 만든 getter와 setter에는 문법적인 오류가 없어야 하며 getter의 이름에 Get을 넣을 필요가 없습니다. 예로, 만약 당신이 owner(소문자, export되지 않았음)라고 불리는 field를 하나 가지고 있다면 getter method는 GetOwner가 아니라 Owner(대문자, export됨)로 호출되어야만 합니다. export하기 위해 대문자 이름을 사용하는 것은 method로 부터 그 field를 구분하기 위한 방법을 제공합니다. 만약 필요하다면, setter 함수는 SetOwner로 불릴 것입니다. 두 이름 모두 읽기에 좋습니다.(getter는 Get이라는 단어를 사용하지 않지만, setter는 Set이라는 단어를 사용함)

```
owner := obj.Owner()
if owner != user {
	obj.SetOwner(user)
}
```

### Interface 이름들 ###
규정에 의하면 하나의 method interface들은 method name에 추가로 -er 접미사를 붙여서 이름을 만듭니다.(예: Reader, Writer, Formatter 등등)

그런류에 많은 이름들이 있고 이런 방법은 그것들을 관리하는데 효율적입니다. Read, Write, Close, Flush, String 등등은 일반적인 용법과 의미를 가지고 있습니다. 혼란을 막기 위해, 같은 용법과 의미를 가지고 있는게 아니라면 당신이 만든 method의 이름을 그 이름들 중에서 사용하지 마십시요. 반대로 만약 당신이 구현한 method가 잘 알려진 type과 같은 의미를 가지고 있다면, 같은 이름과 용법을 사용하십시요. (예: 당신이 만든 string-converter method는 ToString이 아니라 String이라고 이름지으십시요)

### Mixed Caps ###
마지막으로, Go 언어에서의 규약은 여러 단어를 사용한 이름을 쓰기 위해서는 (`_`)보다는 대소문자 혼합(MixedCaps)을 사용한다는 것 입니다.

---


## Semicolons ##
C언어 처럼 Go언어의 정규화된 문법은 한 문장을 끝내기 위해 세미콜론(;)을 사용합니다. 하지만 C언어와는 다르게 세미콜론들은 소스코드 상에는 나타나지 않습니다. 대신 구문분석기(lexer)는 스캔하듯이 자동으로 세미콜론을 넣기 위해 단순한 규칙을 사용합니다. 그렇기 때문에 코드를 작성하는데 있어 대부분 세미콜론을 사용할 필요가 없습니다.

규칙은 다음과 같습니다. 새로운 라인 앞에 마지막 token이 구분자(int또는 float64, 숫자 또는 문자상수 또는 token의 하나와 같은 기본적인 문자를 포함해서)가 됩니다.

```
break continue fallthrough return ++ -- ) }
```

구문분석기(lexer)는 항상 token뒤에 세미콜론을 추가합니다. 이것은 "만약 새로운 라인이 어떤 token뒤에 온다면 문장의 끝을 의미하며 세미콜론을 추가해라"라고 축약해서 말하는 것과 같습니다.

또한, 세미콜론은 중괄호({}) 앞인 경우는 생략될 수 있습니다. 아래 예에서 보는 것과 같이 세미콜론이 필요하지 않습니다.

```
    go func() { for { dst <- <-src } }()
```

Go언어의 문법에서는 오직 for 루프문에서만 초기화와 condition, continuation 요소를 구분하기 위해 세미콜론을 사용합니다. 또한 한 라인에서 여러개의 문장을 구분하기 위해서는 세미콜론을 사용합니다.

한가지 주의할 점은, 제어문(if, for, switch, select)에서 제어문 다음 라인에 중괄호({)를 사용하면 안된다는 것 입니다. 만약 그렇게 하면 제어문의 중괄호({) 앞에 세미콜론이 추가될 것이며 예상하지 못한 결과를 가져오게 될 것 입니다.

예로 아래와 같이 사용하십시요.

```
if i < f() {
    g()
}
```

다음과 같이 사용하면 안됩니다.

```
if i < f()  // wrong! (<-- lexer는 이부분에 세미콜론을 추가하게 될 것입니다.)
{           // wrong!
    g()
}
```


---

## 제어구조(Control structures) ##
Go언어의 제어문은 C와 연관성이 있지만 중요한 차이가 있습니다. Go언어의 제어문에는 do와 while 루프가 없습니다. 오직 좀 더 일반화된 for 루프와 switch문이 있으며 더 탄력적입니다. if와 switch문은 for루프와 같이 추가적인 초기화를 지원하며, type switch와 multiway communications multiplexer, select와 같은 추가적인 새로운 제어문이 있습니다. 문법 또한 조금 다릅니다. 괄호()는 필요하지 않으며 body는 항상 중괄호({})로 구분되어져야 합니다.

### If ###
Go 언어에서 if문의 간단한 예는 다음과 같습니다

```
if x > 0 {
    return y
}
```

중괄호({})의 의무적인 사용은 다중 라인에서 if 문을 단순하게 만들어줍니다. 특히, body안에 return이나 break같은 제어문을 포함하고 있는 경우에는 괜찮은 방법입니다.

if나 switch 문이 초기화를 지원하기 때문에, if나 switch문에서 지역변수를 설정하기 위해 이 방법을 사용하는 것을 쉽게 발견할 수 있습니다.

```
if err := file.Chmod(0664); err != nil {
    log.Print(err)
    return err
}
```

Go언어의 라이브러리에서 if문이 다음 명령문으로 진행되지 않는 경우(break, continue, goto 또는 return 때문에 body가 종료되는 상황) 불필요한 else는 생략되는 것을 발견하게 될 것 입니다.

```
f, err := os.Open(name, os.O_RDONLY, 0)
if err != nil {
    return err
}
codeUsing(f)
```


다음은 일련의 error에 대응하기 위해 code가 보호되어야 하는 일반적인 상황에 대한 예제입니다. 만약 제어의 흐름이 성공적이라면 그 코드는 잘 동작할 것이고, error가 발생할 때마다 error를 제거할 것입니다. error는 return문에서 발생하는 경향이 있기 때문에 결과 code는 else문이 필요 없습니다.

```
f, err := os.Open(name, os.O_RDONLY, 0)
if err != nil {
    return err
}
d, err := f.Stat()
if err != nil {
    return err
}
codeUsing(f, d)
```


### For ###

Go언어에서 for 루프는 C언어의 for 루프와 비슷하지만 같지는 않습니다. for문과 while문은 구분되어지며 do-while문은 없습니다. 세가지 형태가 있으며 그중 하나만 세미콜론을 가지고 있습니다.

```
// C언어와 유사한 형태의 for문
for init; condition; post { }

// C언어와 유사한 형태의 while문
for condition { }

// C언어와 유사한 형태의 for(;;)
for { }
```

루프에서는 짧은 선언이 index 변수를 선언하기 쉽게 만듭니다.
```
sum := 0
for i := 0; i < 10; i++ {
    sum += i
}
```

만약 당신이 배열,slice, string, map, reading을 넘어서 루프를 돌고 있다면, range문이 당신을 대신해서 루프를 관리해줄 것 입니다.

```
var m map[string]int
sum := 0
for _, value := range m {  // key is unused
    sum += value
}
```

string의 경우, range문은 UTF-8 파싱에 의한 개별적인 유니코드 문자를 해결하는데 더욱 유익할 것입니다. 잘못된 encoding은 하나의 byte를 제거하고 U+FFFD 문자로 대체할 것입니다.

아래 루프는
```
for pos, char := range "日本語" {
    fmt.Printf("character %c starts at byte position %d\n", char, pos)
}
```

다음과 같이 출력됩니다.
```
character 日 starts at byte position 0
character 本 starts at byte position 3
character 語 starts at byte position 6
```

마지막으로 Go언어는 콤마(,) 오퍼레이터가 없으며 ++, --는 표현식이 아니라 명령문입니다. 그러므로 만약 for문 안에서 다중 변수(multiple variable)를 사용하려 한다면 병렬 할당(parallel assignment)을 사용해야만 합니다.

```
// Reverse a
for i, j := 0, len(a)-1; i < j; i, j = i+1, j-1 {
    a[i], a[j] = a[j], a[i]
}
```


### Switch ###
Go언어에서 switch문은 C언어에서 보다 더 일반적입니다. 표현식은 상수이거나 integer일 필요가 없습니다. case문은 맨위에서 밑에까지 맞는 값을 찾을 때까지 계속 값을 비교할 것입니다. 그리고 switch문에서는 switch문을 true로 하는 표현식이 없기 때문에 switch문 대신 if-else-if-else 체인을 사용하는 것도 가능합니다.

```
func unhex(c byte) byte {
    switch {
    case '0' <= c && c <= '9':
        return c - '0'
    case 'a' <= c && c <= 'f':
        return c - 'a' + 10
    case 'A' <= c && c <= 'F':
        return c - 'A' + 10
    }
    return 0
}
```

Go언어에서 switch문의 경우 자동으로 통과하는 방법을(case문을 통과하는 경우를 말함) 사용할 수 없지만 콤마(,)를 사용하여 아래 예문과 같이 각 case를 구분할 수 있습니다.

```
func shouldEscape(c byte) bool {
    switch c {
    case ' ', '?', '&', '=', '#', '+', '%':
        return true
    }
    return false
}
```

여기에 두개의 switch문을 사용하여 byte 배열을 비교하는 루틴의 예가 있습니다.

```
// Compare 함수는 두 byte의 배열들을 비교하여 하나의 integer값을 리턴해준다.
// 결과값은 만약 a == b라면 0, a < b라면 -1, a > b라면 +1을 리턴할 것이다.
func Compare(a, b []byte) int {
    for i := 0; i < len(a) && i < len(b); i++ {
        switch {
        case a[i] > b[i]:
            return 1
        case a[i] < b[i]:
            return -1
        }
    }
    switch {
    case len(a) < len(b):
        return -1
    case len(a) > len(b):
        return 1
    }
    return 0
}
```

또한, switch문은 동적인 형태의 interface 변수를 찾는데 사용할 수 있습니다. 그런 형태의 switch문은 괄호() 안에서 키워드의 type과 assertion type의 문법을 사용할 수 있습니다. 만약 switch문이 expression에서 변수를 선언했다면, 변수는 각 문장에 대응하는 type을 가지게 될 것입니다.

```
switch t := interfaceValue.(type) {
default:
    fmt.Printf("unexpected type %T", t)  // %T prints type
case bool:
    fmt.Printf("boolean %t\n", t)
case int:
    fmt.Printf("integer %d\n", t)
case *bool:
    fmt.Printf("pointer to boolean %t\n", *t)
case *int:
    fmt.Printf("pointer to integer %d\n", *t)
}
```

---

## 함수(Functions) ##
### 다중 리턴 값(Multiple return values) ###
Go언어에서 특이한 점 중에 하나는 함수와 메소드가 여러개의 값을 리턴할 수 있다는 것입니다. 이런 형태는 C 프로그램에서 몇몇 어색한 구문들을 개선하는데 사용될 수 있습니다.: in-band 에러 리턴(EOF를 위한 -1과 같은) 과 argument의 수정

C언어에서 쓰기 에러는 임시 주소에서 에러 코드와 함께 음의 숫자로 표현됩니다. Go언어에서는 숫자와 에러를 리턴할 수 있습니다:"몇몇 byte를 쓸 수 있지만 그렇게하면 device 전체 공간을 체우게될 수도 있기 때문에 전부 다 쓸 수는 없습니다."
os 패키지에서 `*`File.Write의 signature는 다음과 같습니다.

```
func (file *File) Write(b []byte) (n int, err Error)
```

그리고 문서에서 말하는 것 처럼, 이 함수는 쓰여진 byte의 수와 n != len(b)인 경우 non-nil 에러를 리턴할 것 입니다. 이것은 일반적인 형태입니다; 더 많은 예제를 확인하고 싶으면 error handling section을 참고하십시오.


비슷한 접근법으로 parameter들을 참조하기 위해 리턴 값으로 부터 포인터를 사용하는 것을 피할 수 있습니다. 다음은 byte array의 한 위치로 부터 숫자를 리턴하고 다음 위치로 이동하기 위한 간단한 함수 예제입니다.

```
func nextInt(b []byte, i int) (int, int) {
    for ; i < len(b) && !isDigit(b[i]); i++ {
    }
    x := 0
    for ; i < len(b) && isDigit(b[i]); i++ {
        x = x*10 + int(b[i])-'0'
    }
    return x, i
}
```

입력된 배열에서 숫자를 검색하기 위해 다음과 같이 할 수 있습니다.

```
    for i := 0; i < len(a); {
        x, i = nextInt(a, i)
        fmt.Println(x)
    }
```
### 명명된 파라미터 결과값(Named result parameters) ###
Go 함수에서는 리턴값 또는 parameter 결과값들에 대해 명명하는 것이 가능하며 입력되는 parameter들 처럼 일반적인 변수로 사용하는 것 또한 가능합니다. 리턴값을 명명하게 되면 그 함수가 시작될 때 리턴값은 리턴값의 타입에 대해 zero 값으로 초기화 됩니다. 만약 함수가 argument 없이 리턴문을 실행하게 되면 현재 parameter들의 결과값을 리턴값으로써 사용하게 됩니다.

결과값에 이름을 짓는 것은 필수 사항이 아니지만, 리턴값에 명명하는 것은 코드를 좀 더 간단하고 명료하게 할 수 있습니다.: 만약 위의 예제에서 nextInt의 결과값을 이름을 붙여주게 되면 어떤 int값이 리턴되어 졌는지 확실히 알게 될 것입니다.
```
func nextInt(b []byte, pos int) (value, nextPos int) {
```
위의 예에서 보듯이 이름붙여진 결과값들이 초기화되었고 간소화되었기 때문에, 단순할 뿐만아니라 명확해질 수 있습니다.
여기 자주 사용되는 io.ReadFull의 예가 있습니다.
```
func ReadFull(r Reader, buf []byte) (n int, err os.Error) {
    for len(buf) > 0 && err == nil {
        var nr int
        nr, err = r.Read(buf)
        n += nr
        buf = buf[nr:len(buf)]
    }
    return
}
```
### Defer ###
Go 언어에서 defer 문은 함수가 지연된 리턴값을 실행하기 전에 즉시 실행될 수 있도록 하나의 function call을 예약합니다.(the deferred function)
일반적이지는 않지만 어떤 경로의 함수가 값을 리턴하는지에 관계없이 자원을 해제해야만하는 상황을 다루기 위해서는 효과적인 방법입니다.전형적인 예로 mutex를 해제하거나 file을 닫는 경우 입니다.
```
// Contents returns the file's contents as a string.
func Contents(filename string) (string, os.Error) {
    f, err := os.Open(filename, os.O_RDONLY, 0)
    if err != nil {
        return "", err
    }
    defer f.Close()  // f.Close()는 현재의 함수가 종료되면 실행되어 질 것입니다.

    var result []byte
    buf := make([]byte, 100)
    for {
        n, err := f.Read(buf[0:])
        result = append(result, buf[0:n]...) // append와 관련해서는 추후에 다루게 될 것입니다.
        if err != nil {
            if err == os.EOF {
                break
            }
            return "", err  // 여기서 리턴한다면 f는 닫히게 될 것입니다.
        }
    }
    return string(result), nil // 여기서 리턴한다면 f는 닫히게 될 것입니다.
}
```

Close와 같은 함수에서 지연 호출은 2가지 장점이 있습니다. 첫번째로, 파일을 닫는 것을 잊지 않도록 해 줄 것입니다.(만약 함수에서 뒤에 파일 닫는 부분을 작성한다면 잊기 쉽습니다.) 두번째로, 이것은 close가 open 근처에 놓이게 된다는 것을 의미하며 함수의 뒷쪽에 놓이는 것보다 훨씬 더 분명해 보입니다.
지연 함수(the deferred function)에서 인자값들은(만약 함수가 메소드라면 수신자(receiver)를 포함해서) 호출이 실행될 때가 아니라 defer문이 실행되어 질 때 평가되어 집니다. 반면, 함수 실행시에 변수값들의 변화에 대한 문제를 피한다는 측면에서, 이것은 하나의 지연된 호출이 여러개의 함수 실행을 지연시킬 수 있다는 의미를 가집니다.
여기 아주 쉬운 예가 있습니다.

```
for i := 0; i < 5; i++ {
    defer fmt.Printf("%d ", i)
}
```

지연된 함수들은 LIFO의 순서로 실행되어 집니다. 그러므로 이 코드는 함수가 종료되었을 때 4 3 2 1 0의 순서로 프린트되어질 것입니다.좀 더 괜찮은 예로 프로그램을 통해 함수 실행을 추적해보는 쉬운 방법이 있습니다. 우리는 다음과 같이 단순히 경로를 추적하는 몇개의 코드를 작성할 수 있습니다.
```
func trace(s string)   { fmt.Println("entering:", s) }
func untrace(s string) { fmt.Println("leaving:", s) }

// Use them like this:
func a() {
    trace("a")
    defer untrace("a")
    // do something....
}
```

우리는 지연된 함수에서 인자값들이 defer문이 실행되어질 때 평가되어진다는 사실을 이용해서 더 좋은 결과를 얻을 수 있습니다. 경로 추적은 루틴에 인자값을 정함으로서 할 수 있습니다.

```
func trace(s string) string {
    fmt.Println("entering:", s)
    return s
}

func un(s string) {
    fmt.Println("leaving:", s)
}

func a() {
    defer un(trace("a"))
    fmt.Println("in a")
}

func b() {
    defer un(trace("b"))
    fmt.Println("in b")
    a()
}

func main() {
    b()
}
prints

entering: b
in b
entering: a
in a
leaving: a
leaving: b
```
프로그래머들이 다른 언어에서는 차단되어져있는(block-level) 자원의 관리에 익숙하기 때문에, defer문은 이상하게 보일지도 모릅니다.하지만 defer문의 가장 흥미롭고 강력한 적용은 block기반이 아니라 function 기반으로부터 온다는 것 입니다. 혼란과 복구(panic and recover) 부분에서 우리는 defer문의 또 다른 예를 보게 될 것 입니다.

---

## 데이타(Data) ##
### new()를 사용한 메모리 할당(Allocation with new()) ###
Go언어에서는 new()와 make(), 두가지 메모리 할당 원형이 있습니다. new()와 make()는 서로 다르게 동작하며 다른 형태로 적용되어 지기 때문에 혼란스러울 수 있습니다. 하지만 규칙은 단순합니다. 먼저 new()에 대해서 얘기하자면, new()는 다른 언어들에서와 마찬가지로 기본적으로 탑재되어 있는 함수 입니다.: new(T)는 새로운 아이템 T를 위해 크기 0의 저장공간을 할 당할 것이며, `*T` 타입의 값을 가지고 있는 할당된 공간의 주소를 리턴할 것입니다. Go 용어로 말하자면, new(T)는 새로 할당된 0 값의 타입 T에 대한 포인터를 리턴할 것입니다.

new()에 의해 반환된 메모리는 0 이기 때문에 객체는 추가적인 초기화없이 사용될 수 있습니다. 이것은 자료구조의 사용자가 new()로 하나를 새롭게 만들 수 있고 바로 작업할 수 있다는 것을 의미합니다. 예를 들어, bytes.Buffer문을 위해 주석을 단다면 "0값의 Buffer는 사용준비가 되어진 비어있는 buffer 입니다"라고 할 수 있습니다. 비슷하게, sync.Mutex는 분명한 생성자나 초기화 메소드를 가지고 있지 않습니다. 대신 sync.Mutex를 위한 0값은 mutex를 해제하기 위해 정의되어져 있습니다.

zero 값이 유용하다는 속성은 타동적으로 동작합니다. 이런 형태의 정의를 고려해보십시오.

```
type SyncedBuffer struct {
    lock    sync.Mutex
    buffer  bytes.Buffer
}
```

SyncedBuffer 타입의 값들은 할당 또는 정의된 즉시 사용할 준비가 되어져 있습니다. 다음에서 p와 v는 추가적인 처리 없이 정확히 동작할 것입니다.

```
p := new(SyncedBuffer)  // type *SyncedBuffer
var v SyncedBuffer      // type  SyncedBuffer
```

### 생성자와 복합문장(Constructors and composite literals) ###
때때로, zero 값으로는 충분하지 않기 때문에 여기있는 os package로 부터 파생된 예처럼 초기화 생성자가 필요할 때가 있습니다.

```
func NewFile(fd int, name string) *File {
    if fd < 0 {
        return nil
    }
    f := new(File)
    f.fd = fd
    f.name = name
    f.dirinfo = nil
    f.nepipe = 0
    return f
}
```

위 예에는 많은 정보가 들어있습니다. 우리는 매번 그 값이 계산될 때 마다 새로운 인스턴스가 생성되는 표현인 복합문장(composite literal)을 사용해서 그것을 단순화시킬 수 있습니다.

```
func NewFile(fd int, name string) *File {
    if fd < 0 {
        return nil
    }
    f := File{fd, name, nil, 0}
    return &f
}
```

C언어에서와는 다르게, 지역 변수의 주소를 반환하는것은 괜찮습니다. 변수의 저장소는 함수가 반환된 뒤에도 살아있을 것입니다. 사실, 복합문장의 주소를 가져가는 것은 그것이 계산될 때마다 새로운 인스턴스를 할당하기 때문에 우리는 위 예에서 마지막 두 줄을 다음과 같이 섞어서 사용할 수 있습니다.

```
    return &File{fd, name, nil, 0}
```

복합문장의 필드는 순서대로이며 반드시 모두 있어야 합니다. 하지만, **_필드:필드값_** 처럼 명확히 요소들을 라벨링한 경우는 초기화는 어떤 순서로든 사용될 수 있습니다.  몇가지는 생략도 가능하며, 생략된 경우는 zero값으로 초기화됩니다.
그러므로 위의 예는 다음과 같이 사용 될 수 있습니다.

```
    return &File{fd: fd, name: name}
```

제한된 경우로, 만약 복합문장이 어떤 필드도 가지지 않은 경우, 그 타입에 대해 zero 값을 생성할 것입니다. new(File)과 &File()의 표현식은 둘다 동일합니다.

복합 문장들은 또한 array, slice, map을 위해 사용될 수 있으며, 이 경우도 인덱싱된 필드라벨 또는 map 키를 함께 사용할 수 있습니다. 아래 예들의 경우, Enone, Eio, Einval이 구분되어지기만 한다면 가지고 있는 값에 상관없이 정상적으로 초기화 될 것입니다.

```
a := [...]string   {Enone: "no error", Eio: "Eio", Einval: "invalid argument"}
s := []string      {Enone: "no error", Eio: "Eio", Einval: "invalid argument"}
m := map[int]string{Enone: "no error", Eio: "Eio", Einval: "invalid argument"}
```

### make()를 사용한 메모리 할당(Allocation with make()) ###
메모리 할당으로 다시 돌아가보면, 내장함수 make(T, args)는 new(T)와는 다른 기능을 제공합니다. make()는 slice, map, channel을 만드는 용도로만 사용됩니다. 또, `*T`가 아니라 T 타입의 초기 값(초기값은 0이 아님)을 반환합니다. 구분해야하는 이유는 숨겨져있고, 사용전에 반드시 초기화되어야 하는 자료 구조를 참조하는 이러한 세가지 형태가 있기 때문입니다.. slice를 예로 들어보면, slice는 데이타(배열 내부)에 대한 포인터, 길이, 용량의 세가지 아이템을 가지고 있는 descriptor이고, 아이템들이 초기화되기 전까지 slice는 nil입니다. make()는 slice, map, channel의 내부 데이터 구조를 초기화하고 사용할 수 있도록 해줍니다.
예를 들면,

```
make([]int, 10, 100)
```

위의 예문은 100개의 int 배열을 위해 메모리 할당을 하고 배열의 처음 10개 요소를 가리키는 길이 10, 용량 100을 가지는 slice 구조를 만듭니다.(slice를 만들 때, 용량은 생략될 수 있습니다.; 더 많은 정보를 원하면 slice 섹션을 보기 바랍니다.) 반면, {{{new([.md](.md)int)는 새롭게 할당된, 0값의 slice 구조에 대한 포인터를 반환합니다. (즉, nil slice에 대한 포인터를 의미함)

다음 예제는 new()와 make()의 차이점에 대해서 설명해줍니다.

```
var p *[]int = new([]int)       // slice구조를 할당함; *p == nil; 그다지 유용하지 않음
var v  []int = make([]int, 100) // slice v는 이제 100개의 int를 가지는 새로운 배열을 참조함

// 불필요하게 복잡함:
var p *[]int = new([]int)
*p = make([]int, 100, 100)

// 자연스러움:
v := make([]int, 100)
```

꼭 기억해야할 것은 make()는 오직 map, slice, channel을 위해서만 적용가능하다는 것이며, 포인터를 반환하지 않습니다. 따라서, 분명한 포인터 값을 얻기 위해서는 new()를 사용해야 합니다.

### 배열(Arrays) ###
배열은 메모리의 자세한 레이아웃을 설계할 때 유용하며, 때로는, 메모리 할당하는 것을 피할 수 있습니다. 하지만 주로 배열은 다음 섹션의 주제인 slice를 위한 하나의 덩어리 입니다. 주제에 대한 기초를 쌓기 위해, 여기서 배열에 관해 몇마디 하자면, Go 언어와 C언어 사이에는 큰 차이 점이 있습니다. Go 언어에서는 배열은 값들입니다. 모든 요소들의 또다른 복사본을 위해 하나의 배열을 지정하는 것, 특히, 만약 함수에 배열을 넘긴다면, 함수는 실제 배열에 대한 포인터가 아니라 배열의 복사본을 넘겨 받게 될 것입니다. 배열의 크기는 배열의 타입의 한 부분입니다. [10](10.md)int 와 [20](20.md)int는 구별되어지며, 값 속성은 유용하지만 사용이 힘듭니다.; 만약 C언어에서와 같은 형태의 사용이나 효율성을 원한다면 다음 예제와 같이 배열에 포인터를 넘겨줄 수도 있습니다.

```
func Sum(a *[3]float64) (sum float64) {
    for _, v := range *a {
        sum += v
    }
    return
}

array := [...]float64{7.0, 8.5, 9.1}
x := Sum(&array)  // Note the explicit address-of operator
```

하지만 이런 형태는 자연스러운 Go언어의 형태가 아닙니다.

### Slices ###
slice는 연속적인 자료에 대해 더 일반화하고, 강력하며, 편리한 인터페이스를 제공하기 위해 배열을 둘러쌉니다. 변환 행렬 같은 명확한 크기를 갖는 아이템을 제외하고, Go에서 대부분의 배열 프로그램은 단순 배열 보다는 slice를 사용합니다.

slice는 참조 타입입니다. 즉, 만약 하나의 slice를 다른쪽에 지정하면 둘다 같은 배열을 참조하는 것을 의미합니다. 예를 들어, 만약 함수가 slice 인자를 받는다면, 값이 변경되는 경우 slice의 요소들을 호출하는 쪽에서 볼 수 있을 것입니다.(배열의 포인터를 넘겨주는 것과 유사합니다.) 따라서, Read 함수는 포인터나 count를 인자로 받기 보다 slice를 인자로 받는 것이 좋습니다. slice 내부의 길이는 얼마나 많은 data를 읽을 지에 대한 제한이 있습니다. 여기에 예로,  os package에서 File 타입의 Read 메소드의 특징이 있습니다.

```
func (file *File) Read(buf []byte) (n int, err os.Error)
```

위의 메소드는 읽은 byte의 숫자와 error값을 반환합니다. 더 큰 buffer b로 처음 32byte를 읽기위해 buffer를 분할합니다.

```
    n, err := f.Read(buf[0:32])
```

이런 분할은 매우 일반적이며 효율적입니다. 사실, 잠시 효율성을 떠나서, 이 조각은 buffer의 처음 32byte를 읽을 것입니다.

```
    var n int
    var err os.Error
    for i := 0; i < 32; i++ {
        nbytes, e := f.Read(buf[i:i+1])  // Read one byte.
        if nbytes == 0 || e != nil {
            err = e
            break
        }
        n += nbytes
    }
```

slice의 길이는 배열의 크기 제한이 허용하는 한도 내에서 변화될 것 입니다.
; slice를 지정하십시오. 내장함수인 cap을 사용해 slice의 최대 용량을 알 수 있습니다. 여기 slice에 data를 추가하는 예가 있습니다. 만약 data가 slice의 용량을 초과한다면, slice는 재할당될 것입니다. 그 결과로 재할당된 slice가 반환됩니다. 아래 예제 함수에서는 len()과 cap()이 nil slice에도 사용이 가능하다는 점을 사용하고 있습니다.(nil slice인 경우 0을 반환함)

```
func Append(slice, data[]byte) []byte {
    l := len(slice)
    if l + len(data) > cap(slice) {  // 재할당
        // 공간을 더 키우기 위해 두배로 할당
        newSlice := make([]byte, (l+len(data))*2)
        // copy 함수는 미리 선언되어져있으며 어떤 slice 타입이나 동작함
        copy(newSlice, slice)
        slice = newSlice
    }
    slice = slice[0:l+len(data)]
    for i, c := range data {
        slice[l+i] = c
    }
    return slice
}
```

반드시 뒤에 slice를 리턴해야만 합니다. 왜냐하면 Append()가 slice의 요소들을 수정할 수 있다하더라도 slice 자체는(포인터, 길이, 용량을 가지고 있는 run-time 자료구조) 값으로 넘겨져야 합니다.

slice에 data를 추가하는 아이디어는 꽤 유용하며, append 함수는 내장 함수로 포함되어 있습니다. append 함수의 설계를 이해하기 위해서는 좀 더 많은 정보가 필요하기 때문에 추후에 다시 살펴보겠습니다.

### Maps ###
Map은 서로 다른 타입의 값들에 접근하기 위한 편리하고 강력한 자료구조 입니다. Key는 동등연산자(==)이 가능한 integer, float, 복소수, string, 포인터, 그리고 인터페이스(dynamic 타입이 동등연산을 지원한다면)와 같이 어떤 타입이든 될 수 있습니다. 반면, struct와 배열 그리고 slice는 동등연산을 할 수 없기 때문에 map key로는 사용할 수 없습니다. slice처럼 map은 참조타입 입니다. 함수에 map을 값으로 넘기고 함수 안에서 변경하게 되면, map 값의 변경사항을 호출자가 확인할 수 있을 것 입니다.

map은 일반적으로 콜론으로 구분되어지는 key값들을 사용해 생성하게 됩니다. 즉, map을 초기화하여 만드는 것은 쉽습니다.

```
var timeZone = map[string] int {
    "UTC":  0*60*60,
    "EST": -5*60*60,
    "CST": -6*60*60,
    "MST": -7*60*60,
    "PST": -8*60*60,
}
```

map의 값을 지정하고 불러오는 것은 인덱스에 정수를 사용하지 않는 것 말고는 배열과 동일한 것 처럼 보입니다.
```
offset := timeZone["EST"]
```

map에서 map에 존재하지 않는 key를 사용해 값을 불러오려고 시도하면 0 값을 반환할 것 입니다. 예를 들면, map이 integer값을 포함하고 있다면, 존재하지 않는 key 값으로 검색을 하면 0을 반환하게 됩니다. 집합(set)은 bool 타입의 map으로 구현될 수 있습니다. 집합에 포함시키기 위해 map 항목의 값을 true로 정하면 됩니다. 그리고 인덱스로 테스트 해보십시오.

```
attended := map[string] bool {
    "Ann": true,
    "Joe": true,
    ...
}

if attended[person] { // will be false if person is not in the map
    fmt.Println(person, "was at the meeting")
}
```

때로는 zero 값을 사용해서 빠진 항목을 구분해야할 필요가 있습니다. (위 예제에서) UTC라는 항목이 있는지 또는 그것이 map 안에 전혀 없기 때문에 값이 zero 값인지 다중 할당(multiple assignment)을 사용해서 구분할 수 있습니다.

```
var seconds int
var ok bool
seconds, ok = timeZone[tz]
```

이 것은 "comma ok"라고 불려집니다. 예를 들어, 만약 tz가 존재한다면, seconds는 적절한 값이 들어갈 것이고 ok는 true가 될 것 입니다. 만약 그렇지 않다면, seconds는 zero값이 될 것이고 ok는 false가 될 것 입니다. 여기 그 두가지를 함께 사용한 error report 함수 예제가 있습니다.

```
func offset(tz string) int {
    if seconds, ok := timeZone[tz]; ok {
        return seconds
    }
    log.Println("unknown time zone:", tz)
    return 0
}
```

실제 값에 대해 map 안에 존재하는지 여부를 시험하기 위해서, blank 식별자나 underscore(`_`)를 사용할 수도 있습니다. blank 식별자는 지정되거나(assign) 또는 어떤 타입의 값으로든 선언될 수 있습니다. map에 존재하는지 테스트하기 위해서 일반적인 변수를 사용하는 곳에 blank 식별자를 사용해 보십시오.

```
_, present := timeZone[tz]
```

map의 항목을 삭제하기 위해서는, 다중 할당(multiple assignment)에서 위치를 서로 바꾸고, boolean 값을 오른쪽에 위치시키면 됩니다.(아래 예와 같이)
만약 boolean 값이 false라면 그 항목은 삭제될 것입니다. 심지어, map에서 이미 그 key가 삭제되었다고 하더라도 이렇게 하는 것은 안전합니다.
```
timeZone["PDT"] = 0, false  // Now on Standard Time
```
### 출력 (Printing) ###
Go언어에서 일반적인 출력은 C언어에서 printf와 비슷한 형태를 사용합니다. 하지만 더 많은 옵션을 제공하며 일반화되어 있습니다. 출력 관련 함수들은 fmt 펙케지 안에 있으며 대문자 이름으로 되어 있습니다: fmt.Printf, fmt, Fprintf, fmt.Sprintf 등등. string 관련 함수들(Sprintf 등)은 제공된 버퍼를 체우기 보다는 string을 반환합니다.

형식적인 string을 제공할 필요가 없습니다. 각 Printf, Fprintf, Sprintf 함수에는 또 다른 쌍의 함수가 있습니다. 예를 들면, Print와 Println 입니다. 이러한 함수들은 형식화된 string을 취하지 않지만 대신, 각각의 인자로 기본 형태의 string을 만듭니다. Println 함수는 인자와 새로운 라인을 추가하는 사이에 빈공간을 추가합니다. 반면, Print 함수의 경우는 오직 양쪽 피연산자가 둘다 string이 아닌 경우만 blank를 추가 합니다. 여기 예제에서 각 라인은 같은 결과를 출력합니다.

```
fmt.Printf("Hello %d\n", 23)
fmt.Fprint(os.Stdout, "Hello ", 23, "\n")
fmt.Println("Hello", 23)
fmt.Println(fmt.Sprint("Hello ", 23))
```

tutorial에서 말했던 것 처럼, fmt.Fprint와 그와 비슷한 함수들은 io.Writer 인터페이스를 구현하는 어떤 오브젝트의 첫번째 인자처럼 받아들입니다. os.Stdout과 os.Stderr 변수들과 비슷한 경우 입니다.

여기 있는 예제는 C언어와 달라지기 시작하는 것을 보여줍니다. 첫번째로, %d와 같은 숫자 형식은 부호나 크기를 위한 flag를 취하지 않습니다. 대신 출력 루틴은 이런 속성들을 결정하기 위해 인자 타입을 사용합니다.
```
var x uint64 = 1<<64 - 1
fmt.Printf("%d %x; %d %x\n", x, x, int64(x), int64(x))
prints

18446744073709551615 ffffffffffffffff; -1 -1
```

만약 10진수 정수와 같은 기본 변환을 원한다면, 포괄적인 형태의 %v를 사용할 수 있습니다.(값을 위해서) 결과는 Print와 Println이 만들어 내는 것과 정확히 같습니다. 더욱이 그런 형식으로 어떤 값이든 출력할 수 있습니다.(심지어 배열, 구조체,map 까지도) 여기 앞의 예제에 정의되어 있는 time zone map을 사용하는 print문 예제가 있습니다.

```
fmt.Printf("%v\n", timeZone)  // or just fmt.Println(timeZone)

출력:
map[CST:-21600 PST:-28800 EST:-18000 UTC:0 MST:-25200]
```

물론, map에서 key는 순서없이 출력될 것 입니다. 구조체를 출력할 때, 변형 포맷인 %+v는 구조체 필드의 이름을 표시할 것입니다. 그리고 어떤 값에 대해서든 %#v 형식은 전체 Go 구문을 출력할 것 입니다.

```
type T struct {
    a int
    b float
    c string
}
t := &T{ 7, -2.35, "abc\tdef" }
fmt.Printf("%v\n", t)
fmt.Printf("%+v\n", t)
fmt.Printf("%#v\n", t)
fmt.Printf("%#v\n", timeZone)
prints

&{7 -2.35 abc   def}
&{a:7 b:-2.35 c:abc     def}
&main.T{a:7, b:-2.35, c:"abc\tdef"}
map[string] int{"CST":-21600, "PST":-28800, "EST":-18000, "UTC":0, "MST":-25200}
```
(&기호에 주목하십시오.) 인용부호로("") 둘러쌓인 문자열 형식은 string 또는 byte 배열([.md](.md)byte) 형태의 값을 적용할 때 %q를 통해서도 가능 합니다. %#q 형식은 만약 가능하다면 인용부호("") 대신 backquote(`)를 사용할 것 입니다. 또한 %x 옵션은 integer뿐만 아니라 문자열과 byte 배열에서도 동작 합니다. 그것은 긴 16진 문자열을 생성하며, %x에서 space를 사용하는 것(% x)은 byte 사이에 space를 넣게 됩니다.

또다른 유용한 형태는 값의 타입을 출력하는 %T 옵션 입니다.

```
fmt.Printf("%T\n", timeZone)
prints

map[string] int
```

만약 custom 타입을 기본 형태로 제어하기를 원한다면, 모든 요구사항은 String() string 메소드에 그 타입을 정의해야 합니다.
T 타입은 다음 예제와 같이 보여질 것 입니다.

```
func (t *T) String() string {
    return fmt.Sprintf("%d/%g/%q", t.a, t.b, t.c)
}
fmt.Printf("%v\n", t)
to print in the format

7/-2.35/"abc\tdef"
```

String() 메소드는 Sprintf를 호출 하는 것이 가능합니다. 왜냐하면 print 루틴은 재귀적으로 사용될 수 있기 때문입니다.

이제 한단게 더 나갈 수 있습니다. 그리고 print 루틴의 인자들을 바로 다른 루틴에 넘겨줄 수 있습니다. Printf 함수의 특징은 포맷 이후에 나타날 수도 있는 임의의 개수의 파라미터(또는 임의의 형태의 파라미터)들을 명시하기 위해 마지막 인자로 `...interface{`} 타입을 사용한다는 것 입니다.

```
func Printf(format string, v ...interface{}) (n int, errno os.Error) {
```

Printf 함수 내부에서, v는 [.md](.md)interface{} 타입의 변수처럼 동작합니다. 하지만 만약 또 다른 가변인자 함수로 넘겨진다면, 그것은 일반적인 인자 리스트 처럼 동작할 것 입니다. 여기 log.Println을 구현한 예제가 있습니다.(위에서 사용한) 이 함수는 실제 설정을 위해 인자값들을 바로 fmt.Sprintln 함수로 넘깁니다.

```
// Println prints to the standard logger in the manner of fmt.Println.
func Println(v ...interface{}) {
    std.Output(2, fmt.Sprintln(v...))  // Output takes parameters (int, string)
}
```

인자 리스트로서 v를 취급하도록 컴파일러에 알려주기 위해, Sprintln에 대한 내부 호출에서 v 뒤에 `...`을 사용 합니다. 그렇지 않다면, 단순히 v를 싱글 slice 인자로 넘겨줄 것 입니다.

출력 관련해서는 이 문서에서 다루고 있는 것 이상의 내용이 있습니다. fmt 팩키지에 대한 더 자세한 내용을 알고 싶다면 godoc 문서를 보십시오.

어쨌거나 `...` 파라미터는 특정 타입이 될 수 있습니다. 예를 들면 integer 리스트에서 최소값을 고르는 min 함수를 위해 `...int`를 사용할 수 있습니다.

```
func Min(a ...int) int {
    min := int(^uint(0) >> 1)  // largest int
    for _, i := range a {
        if i < min {
            min = i
        }
    }
    return min
}
```

### 덧붙임(Append) ###
이제 내장함수인 append 함수에 대해 설명하도록 하겠습니다. append 함수의 특징은 위에 나온 기존의 append 함수와는 좀 다릅니다. 대략 다음과 같습니다:

```
func append(slice []T, elements...T) []T
```

T는 어떤 주어진 타입에 대한 위치표시(placeholder) 입니다. 사실 Go언어에서 호출자에 의해 T 타입이 결정되어지는 함수는 사용할 수 없습니다. 이 것이 append가 내장함수인 이유입니다.: 그것은 컴파일러의 지원이 필요합니다. append가 하는 것은 slice의 끝에 요소(element)를 추가하고 결과를 반환하는 것 입니다. 결과는 반환되어질 필요가 있습니다. 왜냐하면, 우리가 손으로 써서 글에 덧붙이는 것 처럼(append), 원본 배열이 변경될지도 모르기 때문입니다. 여기 간단한 예제가 있습니다.

```
x := []int{1,2,3}
x = append(x, 4, 5, 6)
fmt.Println(x)
```

위의 예제는 `[1 2 3 4 5 6]`을 출력 합니다. 따라서, append는 조금은 Printf의 동작과도 비슷하며, 임의의 개수의 인자들을 취합합니다. 하지만, 만약 slice와 slice를 append하기를 원한다면 어떻게 해야할까요? 쉽습니다: 위의 예제에서 Output을 호출하듯이, 호출하는 쪽에서 `...`을 사용하면 됩니다. 이렇게 하는 것은 위의 예에서 한것과 동일한 결과를 출력합니다.

```
x := []int{1,2,3}
y := []int{4,5,6}
x = append(x, y...)
fmt.Println(x)
```

`...` 없이는 컴파일이 되지 않습니다. 왜냐하면 타입이 잘못되었기 때문입니다. y는 int 타입이 아닙니다.


---

## 초기화(Initialization) ##
비록, C나 C++의 초기화 과정과 표면적으로 아주 다르게 보이는 부분은 없지만, Go 언어의 초기화 과정은 보다 강력합니다. 초기화 과정에서 복잡한 구조체가 만들어지고, 초기화된 Object와 여타 package간의 배치가 보다 명확하게 다루어집니다.

### 상수(Constants) ###
Go 언어에서 상수는 말그대로 상수입니다. 상수는 컴파일 시점에 생성되며, - 심지어 함수의 지역 상수라도 - 그 값은 숫자, 문자열, boolean 만 가능합니다. 컴파일 시점에 생성되는 제약때문에, 상수를 정의하는 표현은 컴파일러가 이해할 수 있는 상수 형태이어야 합니다. 예를 들어 1<<3 은 상수 형태이고, math.Sin(math.Pi/4)는 math.Sin 함수가 런타임에 호출되어야 하므로 상수 형태라고 할 수 없습니다.

Go 언어에서, 열거된 상수는 iota 열거자(iota enumerator)를 사용해서 생성합니다. iota는 expression의 한 부분이 될 수 있고, expression은 무조건적인 반복이 될 수 있기 때문에, 그것은 복잡한 값의 집합이 되기 쉽습니다.

```
type ByteSize float64
const (
    _ = iota  // ignore first value by assigning to blank identifier
    KB ByteSize = 1<<(10*iota)
    MB
    GB
    TB
    PB
    EB
    ZB
    YB
)
```

String과 같은 메소드를 추가하는 기능은 일반적인 타입의 한 부분일지라도, 출력에 대해 그런 값들이 자동으로 형식화되도록 하는 것을 가능하게 합니다.

```
func (b ByteSize) String() string {
    switch {
    case b >= YB:
        return fmt.Sprintf("%.2fYB", float64(b/YB))
    case b >= ZB:
        return fmt.Sprintf("%.2fZB", float64(b/ZB))
    case b >= EB:
        return fmt.Sprintf("%.2fEB", float64(b/EB))
    case b >= PB:
        return fmt.Sprintf("%.2fPB", float64(b/PB))
    case b >= TB:
        return fmt.Sprintf("%.2fTB", float64(b/TB))
    case b >= GB:
        return fmt.Sprintf("%.2fGB", float64(b/GB))
    case b >= MB:
        return fmt.Sprintf("%.2fMB", float64(b/MB))
    case b >= KB:
        return fmt.Sprintf("%.2fKB", float64(b/KB))
    }
    return fmt.Sprintf("%.2fB", float64(b))
}
```

( float64 변환은 Sprintf가 ByteSize에 대해 String 메소드를 통해 반복되는 것을 막아줍니다.) YB 표현식(expression)은 1.00YB로 출력됩니다. 반면 ByteSize(1e13)은 9.09TB로 출력됩니다.

### 변수(Variables) ###
변수는 상수처럼 초기화될 수 있지만 초기화는 런타임(run time)에 계산되는 일반적인 표현식(expression)이 될 수도 있습니다.

```
var (
    HOME = os.Getenv("HOME")
    USER = os.Getenv("USER")
    GOROOT = os.Getenv("GOROOT")
)
```

### 초기화 함수(The init function) ###
마침내, 각 소스파일은 어떤 명령문이던 간에 필요하다면 셋업하기 위해 자신만의 init() 함수를 정의할 수 있습니다. 유일한 제한은 비록, 초기화하는 동안 go 루틴이 시작될 수 있다고 해도, 초기화가 끝날때까지는 실행되지 않을 것이라는 점입니다. 초기화는 항상 단일 스레드로 실행됩니다. 그리고 마지막으로, 팩케지 안에 모든 변수 선언이 초기화된 뒤에 init()이 호출되어 집니다.

선언으로서 표현될 수 없는 초기화에 비해, init() 함수의 일반적인 사용은 실제 실행이 시작되기 전에 검증이나 프로그램 명령문의 정확한 수정을 위해 사용될 수 있습니다.

```
func init() {
    if USER == "" {
        log.Fatal("$USER not set")
    }
    if HOME == "" {
        HOME = "/usr/" + USER
    }
    if GOROOT == "" {
        GOROOT = HOME + "/go"
    }
    // GOROOT may be overridden by --goroot flag on command line.
    flag.StringVar(&GOROOT, "goroot", GOROOT, "Go root directory")
}
```


---


## 메소드(Methods) ##
### 포인터 vs 값(Pointers vs. Values) ###
메소드는 pointer나 interface가 아니라면 어떤 이름의 타입으로든지 정의되어 질 수 있습니다. 수신자(receiver)는 구조체(struct)여서는 안됩니다. 위에 slice에 대해 토론한 부분에서, append 함수에 대해서 언급했었습니다. 대신 우리는 슬라이스에서 그것을 메소드로 정의할 수도 있습니다. 이렇게 하기 위해서, 우리는 먼저 메소드와 묶을 수 있도록 타입을 정의해야 합니다. 그리고, 그 타입의 값인 메소드에 대한 수신자(receiver)를 만들어야 합니다.

```
type ByteSlice []byte

func (slice ByteSlice) Append(data []byte) []byte {
    // Body는 위에서 했던것과 똑같습니다.
}
```

이 예제는 여전히 업데이트된 slice를 반환하기 위한 방법이 필요합니다. 우리는 수신자(receiver)로서 Byteslice의 포인터를 받아들이도록 메소드를 다시 정의함으로써 어색한 부분을 고칠 수 있습니다. 그렇게하면 메소드는 호출자(caller)의 slice를 덮어쓸 수 있습니다.

```
func (p *ByteSlice) Append(data []byte) {
    slice := *p
    // Body는 위와 같지만 return이 없습니다.
    *p = slice
}
```

사실 우리는 이것보다 더 잘할 수 있습니다. 만약 우리가 우리의 함수를 수정한다면 아래 예제처럼 그것은 표준적인 메소드처럼 보일 것 입니다.

```
func (p *ByteSlice) Write(data []byte) (n int, err os.Error) {
    slice := *p
    // 위의 예제와 같음
    *p = slice
    return len(data), nil
}
```

그렇게되면, `*ByteSlice` 타입은 표준 인터페이스인 io.Writer를 만족시키며 편리합니다. 예를들면, 우리는 아래 예처럼 한번에 출력할 수 있습니다.

```
    var b ByteSlice
    fmt.Fprintf(&b, "This hour has %d days\n", 7)
```

우리는 ByteSlice의 주소값을 넘겼습니다. 왜냐하면 `*ByteSlice`만이 io.Writer에 적합하기 때문입니다. 수신자(receiver)에 대한 포인터와 값에 대한 규칙은 값 메소드는(value method) 포인터와 값을 불러올 수 있지만 포인터 메소드는(pointer method) 오로지 포인터만을 불러올 수 있다는 것 입니다. 이것은 포인터 메소드는 수신자(receiver)를 수정할 수 있기 때문입니다. 값의 복사본에서 그것들을 불러오는 경우는 이런 수정들이 적용되지 않을 것 입니다.

byte slice에서 사용되어진 아이디어는 bytes.Buffer에 구현되어 있습니다.


---


## 인터페이스와 기타 타입들(Interfaces and other types) ##
### 인터페이스(Interfaces) ###
Go언어에서 인터페이스(Interface)는 객체(object)의 행동을 정의하기 위한 방법으로 제공됩니다.: 만약 무언가 이렇게 할 수 있다면, 그것은 여기에서 사용되어질 수 있습니다. 우리는 이미 몇개의 예제를 봐왔습니다. Fprintf 함수가 Write 메소드를 사용해서 어떤 것이든 출력할 수 있는 반면, 사용자 출력 함수(custom printer)는 String 메소드에 의해서 구현될 수 있습니다. 하나 또는 두개의 메소드로 구성된 인터페이스는 Go 코드에서 일반적입니다. 그리고 Write를 구현하는 무언가에 대한 io.Writer와 같이 일반적으로 메소드로부터 파생된 이름을 사용합니다.

타입은 다중 인터페이스로 구현되어질 수 있습니다. 예를 들어, 만약 Len(), Less(i,j int) bool, Swap(i, j int)를 포함하고 있는 sort.interface를 구현한다면, 집합은 sort 팩케지에서 루틴(routine)에 의해 정렬되어질 수 있습니다. 그리고 그것은 또한 custom formatter를 가질 수 있습니다. 이런 예제로 Sequence는 양쪽 모두에 적합합니다.

```
type Sequence []int

// sort.Interface의 메소드를 필요로함
func (s Sequence) Len() int {
    return len(s)
}
func (s Sequence) Less(i, j int) bool {
    return s[i] < s[j]
}
func (s Sequence) Swap(i, j int) {
    s[i], s[j] = s[j], s[i]
}

// 출력을 위한 메소드- 출력 전에 요소를 정렬
func (s Sequence) String() string {
    sort.Sort(s)
    str := "["
    for i, elem := range s {
        if i > 0 {
            str += " "
        }
        str += fmt.Sprint(elem)
    }
    return str + "]"
}
```

### 변환(Conversions) ###
Sequence의 String 메소드는 slice에서 이미 Sprint가 한 것을 다시 만듭니다. 만약 우리가 Sprint를 호출하기 전에 Sequence를 plain [.md](.md)int로 변환한다면 우리는 그런 노력을 공유할 수 있습니다.

```
func (s Sequence) String() string {
    sort.Sort(s)
    return fmt.Sprint([]int(s))
}
```

일반적인 slice처럼 다루기 위해서 변환은 s를 불러냅니다. 그리고 기본 형태로 받습니다. 변환이 없다면, Sprint는 Sequence의 String 메소드를 찾게될 것이고 무한정 반복할 것 입니다. 만약 타입의 이름을 우리가 무시한다면 두개의 타입(Sequence와 [.md](.md)int)는 똑같기 때문에 그것들 사이에서 변환하는 것은 정당합니다. 변환은 새로운 값을 생성하지는 않습니다. 그것은 단지 임시로 새로운 타입의 기존에 존재하는 값들을 통해서 동작하는 것 뿐입니다.(integer에서 float변환과 같은 또 다른 정당한 변환이 있습니다. 그것은 새로운 값을 생성합니다.)
그것은 Go 프로그램에서 표현식의 타입을 다른 형태의 메소드에 접근하게 하기 위한 하나의 숙어입니다. 예를 들어, 우리는 전체 예를 줄이기 위해 기존의 sort.IntArray 타입을 사용할 수 있습니다.

```
type Sequence []int

// 출력을 위한 메소드 - 출력 전에 요소들을 정렬
func (s Sequence) String() string {
    sort.IntArray(s).Sort()
    return fmt.Sprint([]int(s))
}
```

이제, Squence가 다중 인터페이스(정렬과 출력)을 구현하는 것 대신에, 우리는 다양한 타입으로(Sequence, sort.IntArray, [.md](.md)int) 변환되는 데이터 아이템의 능력을 사용합니다.그 각각은 전체 일의 몇몇 부분으로 동작합니다. 예제로는 일반적이지 않지만 효율적일 수 있습니다.

### 일반성(Generality) ###
만약 한 타입이 하나의 인터페이스를 구현하기 위해서만 존재한다면 그리고 그 인터페이스 너머로 export되는 메소드가 없다면, 그 타입도 export되어야할 필요가 업습니다. exporting은 단지 인터페이스의 동작을 명확히 해줄 뿐이지 구현이 아니며, 다른 속성을 가진 또 다른 구현들은 원래 타입의 동작을 반영할 수 있습니다. 그것은 또한 일반적인 메소드의 모든 인스턴스(instance)에 대한 문서작업의 반복을 피할 수 있게해줍니다.

그런 경우에, 생성자는 구현된 타입이 아니라 인터페이스 값을 반환해야만 합니다. 예를 들어, crc32.NewIEEE()와 adler32.New() 두 hash 라이브러리는 인터페이스 타입인 hash.Hash32를 반환합니다. Go 프로그램에서 CRC-32 알고리즘을 Adler-32로 대체하는 것은 단지 생성자 호출의 변화만 있을 뿐입니다. 코드의 나머지는 알고리즘의 변화에 영향을 받지 않습니다.

비슷한 방식으로, crypto/block 팩케지의 streaming cipher 알고리즘이 함께 엮여있는 block cipher와 분리되는 것을 허용합니다. bufio 팩케지에서 유추해보면, 그들은 Cipher 인터페이스를 랩하고 특정한 구현이 아니라, hash.Hash, io.Reader, 또는 io.Writer 인터페이스 값을 반환합니다.

crypto/block이 포함된 인터페이스:

```
type Cipher interface {
    BlockSize() int
    Encrypt(src, dst []byte)
    Decrypt(src, dst []byte)
}

// NewECBDecrypter는 r로 부터 data를 읽는 reader를 반환한다.
// 그리고 electronic codebook(ECB) mode로 c를 사용해 복호화 한다.
func NewECBDecrypter(c Cipher, r io.Reader) io.Reader

// NewCBCDecrypter는 r로 부터 data를 읽는 reader를 반환한다.
// 그리고 초기화 벡터 iv와 cipheer block chaining (CBC) mode로 c를 사용해 복호화 한다.
func NewCBCDecrypter(c Cipher, iv []byte, r io.Reader) io.Reader
```

NewECBDecrypter 와 NewCBCReader는 단순히 하나의 특정 암호화 알고리즘이나 데이터 소스에 적용되는 것이 아니라 어떤 암호화 인터페이스나 io.Reader의 구현에 적용할 수 있습니다.. 그들이 io.Reader 인터페이스 값을 반환하기 때문에, ECB 암호화를 CBC 암호화로 대체하는 것은 부분적인 변화입니다. 생성자 호출은 반드시 수정되어야만 하지만 주변 코드들은 io.Reader를 결과값으로 처리해야만 하기 때문에 차이점을 알 수 없을 것입니다.

### 인터페이스와 메소드(Interfaces and methods) ###
거의 대부분 메소드를 가질 수 있기 때문에, 거의 대부분 인터페이스를 만족합니다. 하나 분명한 예는, Handler 인터페이스를 정의하고 있는 http 팩케지 입니다. Handler를 구현하고 있는 어떤 오브젝트(object)도 HTTP 요청을 처리할 수 있습니다.

```
type Handler interface {
    ServeHTTP(ResponseWriter, *Request)
}
```

ResponseWriter는 그 자체로 client의 응답에 대해 리턴을 필요로하는 메소드의 접근을 제공하는 하나의 인터페이스 입니다. 그러한 메소드들은 표준 Write 메소드를 포함하고 있습니다. 따라서 http.ResponseWriter는 io.Writer가 사용될 수 있는 곳이면 어디서나 사용될 수 있습니다. Request는 client로부터의 요청에 대한 parsed representation을 포함하는 구조체 입니다.

간단하게, POST를 무시하고 HTTP라고 가정하면 요청들은 항상 GET 입니다. 그런 단순화는 handler를 설정하는데 영향을 주지 않습니다. 여기 방문한 페이지의 수를 세기 위한 간단하지만 완전한 handler 구현 예제가 있습니다.

```
// Simple counter server.
type Counter struct {
    n int
}

func (ctr *Counter) ServeHTTP(w http.ResponseWriter, req *http.Request) {
    ctr.n++
    fmt.Fprintf(w, "counter = %d\n", ctr.n)
}
```
(우리의 주제를 유지하기 위해, Fprintf가 어떻게 http.ResponseWriter를 출력하는지 주의하십시오) 참고로, 여기 어떻게 그런 서버들을 URL tree의 한 node에 추가하는지에 대한 예제가 있습니다.

```
import "http"
...
ctr := new(Counter)
http.Handle("/counter", ctr)
```

하지만 왜 Counter를 구조체로 만들어야만 할까요? 하나의 integer가 필요한 전부입니다.(receiver는 하나의 포인터를 필요로하고 그렇게 해서 숫자가 증가되는 것이 호출자에게 보여집니다.)

```
// Simpler counter server.
type Counter int

func (ctr *Counter) ServeHTTP(w http.ResponseWriter, req *http.Request) {
    *ctr++
    fmt.Fprintf(w, "counter = %d\n", *ctr)
}
```

만약 당신의 프로그램이 이미 방문한 페이지 임을 알려주기 위한 internal state를 가지고 있다면 어떻게 해야할까요? 웹 페이지를 채널로 묶으면 됩니다.

```
//각 방문에 대해 알람을 보내는 채널
// (Probably want the channel to be buffered.)
type Chan chan *http.Request

func (ch Chan) ServeHTTP(w http.ResponseWriter, req *http.Request) {
    ch <- req
    fmt.Fprint(w, "notification sent")
}
```


마지막으로, /args에 대해 설명하면, 인자값은 server binary를 invoke할 때 사용되어지며 그것은 인자값을 출력하기 위한 함수 작성에 용이합니다.

```
func ArgServer() {
    for i, s := range os.Args {
        fmt.Println(s)
    }
}
```

HTTP server에는 어떻게 적용할 수 있을까요? 우리는 ArgServer를 우리가 무시했던 값들의 몇몇 type의 메소드로 만들 수 있습니다. 하지만 더 깔끔한 방법이 있습니다. 우리는 메소드를 포인터와 인터페이스를 제외한 어떤 type으로든지 정의할 수 있기 때문에, 우리는 함수에 대한 메소드를 작성할 수 있습니다. http package는 다음 코드를 포함하고 있습니다.

```
// The HandlerFunc type is an adapter to allow the use of
// ordinary functions as HTTP handlers.  If f is a function
// with the appropriate signature, HandlerFunc(f) is a
// Handler object that calls f.
type HandlerFunc func(ResponseWriter, *Request)

// ServeHTTP calls f(c, req).
func (f HandlerFunc) ServeHTTP(w ResponseWriter, req *Request) {
    f(w, req)
}
```

HandlerFunc는 ServeHTTP 메소드 타입입니다. 따라서 그런 형태의 값들은 HTTP요청들을 처리할 수 있습니다. 메소드 구현을 보십시오: 리시버는 함수 f 그리고 메소드 콜 f 입니다. 아마도 좀 이상해보일 수 있습니다. 하지만 리시버가 채널이되고 그리고 메소드가 채널로 송신하는 것과 다르지 않습니다.

ArgServer를 HTTP server로 만들기 위해, 우리는 먼저 올바른 signature를 가지도록 수정해야합니다.

```
// Argument server.
func ArgServer(w http.ResponseWriter, req *http.Request) {
    for i, s := range os.Args {
        fmt.Fprintln(w, s)
    }
}
```

ArgServer는 이제 HandlerFunc로써 같은 signature를 갖게됐습니다. 따라서 ArgServer는 메소드에 접근하기 위한 type으로 변환이 가능합니다. 우리는 IntArray에 접근하기 위해 단순히 Sequence를 IntArray로 변환했습니다.설정하기 위한코드는 매우 간단합니다.

```
http.Handle("/args", http.HandlerFunc(ArgServer))
```


누군가 /args 페이지를 방문할 때, 그 페이지에 설치되어 있는 핸들러는 ArgServer와 HandlerFunc 타입의 값을 가지게 됩니다. HTTP server는 리시버로서 ArgServer와 함께 ServeHTTP 메소드를 호출하게 될 것입니다.
which will in turn call ArgServer (via the invocation f(c, req) inside HandlerFunc.ServeHTTP). argument들은 이후에 보여지게 될 것 입니다.

인터페이스는 모든 유형에 대해 정의될 수 있는 메소드 집합이기 때문에, 이 섹션에서 우리는 구조체, integer, channel, 함수 모두를 사용해 HTTP 서버 하나를 만들었습니다.


---

## Embedding ##
Go언어는 전형적인 형태의 subclassing의 개념을 제공하지 않습니다. 하지만 구조체 또는 인터페이스 내에 type들을 포함시켜 구현을 가능하게 할 수 있습니다.
Interface embedding은 매우 단순합니다. 우리는 io.Reader와 io.Writer 인터페이스에 대해 이전에 이미 다루었습니다.; 여기 그것에 대한 definition이 있습니다.

```
type Reader interface {
    Read(p []byte) (n int, err os.Error)
}

type Writer interface {
    Write(p []byte) (n int, err os.Error)
}
```

또한, io package는 메소드와 같이 몇몇 구현 가능한 object들을 명시하는 몇가지 다른 interface들을 export합니다. 예를들어, io.ReadWriter를 보면, interface는 Read와 Write 두가지 모두 포함합니다. 우리는 명확히 두 method를 listing함으로서 io.ReadWriter를 명시할 수 있습니다. 하지만, 다음과 같이 새로운 하나의 form에 두개의 interface를 포함시키는 것이 더 쉽고 좋은 생각입니다.

```
// ReadWriter is the interface that combines the Reader and Writer interfaces.
type ReadWriter interface {
    Reader
    Writer
}
```

This says just what it looks like: A ReadWriter can do what a Reader does and what a Writer does; it is a union of the embedded interfaces (which must be disjoint sets of methods). Only interfaces can be embedded within interfaces.

The same basic idea applies to structs, but with more far-reaching implications. The bufio package has two struct types, bufio.Reader and bufio.Writer, each of which of course implements the analogous interfaces from package io. And bufio also implements a buffered reader/writer, which it does by combining a reader and a writer into one struct using embedding: it lists the types within the struct but does not give them field names.

```
// ReadWriter stores pointers to a Reader and a Writer.
// It implements io.ReadWriter.
type ReadWriter struct {
    *Reader  // *bufio.Reader
    *Writer  // *bufio.Writer
}
```

The embedded elements are pointers to structs and of course must be initialized to point to valid structs before they can be used. The ReadWriter struct could be written as

```
type ReadWriter struct {
    reader *Reader
    writer *Writer
}
```

but then to promote the methods of the fields and to satisfy the io interfaces, we would also need to provide forwarding methods, like this:

```
func (rw *ReadWriter) Read(p []byte) (n int, err os.Error) {
    return rw.reader.Read(p)
}
```

By embedding the structs directly, we avoid this bookkeeping. The methods of embedded types come along for free, which means that bufio.ReadWriter not only has the methods of bufio.Reader and bufio.Writer, it also satisfies all three interfaces: io.Reader, io.Writer, and io.ReadWriter.

There's an important way in which embedding differs from subclassing. When we embed a type, the methods of that type become methods of the outer type, but when they are invoked the receiver of the method is the inner type, not the outer one. In our example, when the Read method of a bufio.ReadWriter is invoked, it has exactly the same effect as the forwarding method written out above; the receiver is the reader field of the ReadWriter, not the ReadWriter itself.

Embedding can also be a simple convenience. This example shows an embedded field alongside a regular, named field.

```
type Job struct {
    Command string
    *log.Logger
}
```

The Job type now has the Log, Logf and other methods of `*log.Logger`. We could have given the Logger a field name, of course, but it's not necessary to do so. And now, once initialized, we can log to the Job:

job.Log("starting now...")
The Logger is a regular field of the struct and we can initialize it in the usual way with a constructor,

```
func NewJob(command string, logger *log.Logger) *Job {
    return &Job{command, logger}
}
```

or with a composite literal,
```
job := &Job{command, log.New(os.Stderr, "Job: ", log.Ldate)}
```

If we need to refer to an embedded field directly, the type name of the field, ignoring the package qualifier, serves as a field name. If we needed to access the `*log.Logger` of a Job variable job, we would write job.Logger. This would be useful if we wanted to refine the methods of Logger.

```
func (job *Job) Logf(format string, args ...) {
    job.Logger.Logf("%q: %s", job.Command, fmt.Sprintf(format, args))
}
```

Embedding types introduces the problem of name conflicts but the rules to resolve them are simple. First, a field or method X hides any other item X in a more deeply nested part of the type. If log.Logger contained a field or method called Command, the Command field of Job would dominate it.

Second, if the same name appears at the same nesting level, it is usually an error; it would be erroneous to embed log.Logger if the Job struct contained another field or method called Logger. However, if the duplicate name is never mentioned in the program outside the type definition, it is OK. This qualification provides some protection against changes made to types embedded from outside; there is no problem if a field is added that conflicts with another field in another subtype if neither field is ever used.

---

## 동시성(Concurrency) ##
### 통신에 의한 공유(Share by communicating) ###
Concurrent programming is a large topic and there is space only for some Go-specific highlights here.

Concurrent programming in many environments is made difficult by the subtleties required to implement correct access to shared variables. Go encourages a different approach in which shared values are passed around on channels and, in fact, never actively shared by separate threads of execution. Only one goroutine has access to the value at any given time. Data races cannot occur, by design. To encourage this way of thinking we have reduced it to a slogan:

Do not communicate by sharing memory; instead, share memory by communicating.
This approach can be taken too far. Reference counts may be best done by putting a mutex around an integer variable, for instance. But as a high-level approach, using channels to control access makes it easier to write clear, correct programs.

One way to think about this model is to consider a typical single-threaded program running on one CPU. It has no need for synchronization primitives. Now run another such instance; it too needs no synchronization. Now let those two communicate; if the communication is the synchronizer, there's still no need for other synchronization. Unix pipelines, for example, fit this model perfectly. Although Go's approach to concurrency originates in Hoare's Communicating Sequential Processes (CSP), it can also be seen as a type-safe generalization of Unix pipes.

### Go루틴(Goroutines) ###
They're called goroutines because the existing terms—threads, coroutines, processes, and so on—convey inaccurate connotations. A goroutine has a simple model: it is a function executing in parallel with other goroutines in the same address space. It is lightweight, costing little more than the allocation of stack space. And the stacks start small, so they are cheap, and grow by allocating (and freeing) heap storage as required.

Goroutines are multiplexed onto multiple OS threads so if one should block, such as while waiting for I/O, others continue to run. Their design hides many of the complexities of thread creation and management.

Prefix a function or method call with the go keyword to run the call in a new goroutine. When the call completes, the goroutine exits, silently. (The effect is similar to the Unix shell's & notation for running a command in the background.)
```
go list.Sort()  // run list.Sort in parallel; don't wait for it. 
```
A function literal can be handy in a goroutine invocation.

```
func Announce(message string, delay int64) {
    go func() {
        time.Sleep(delay)
        fmt.Println(message)
    }()  // Note the parentheses - must call the function.
}
```

In Go, function literals are closures: the implementation makes sure the variables referred to by the function survive as long as they are active.

These examples aren't too practical because the functions have no way of signaling completion. For that, we need channels.

### 채널(Channels) ###
Like maps, channels are a reference type and are allocated with make. If an optional integer parameter is provided, it sets the buffer size for the channel. The default is zero, for an unbuffered or synchronous channel.

```
ci := make(chan int)            // unbuffered channel of integers
cj := make(chan int, 0)         // unbuffered channel of integers
cs := make(chan *os.File, 100)  // buffered channel of pointers to Files
```

Channels combine communication—the exchange of a value—with synchronization—guaranteeing that two calculations (goroutines) are in a known state.

There are lots of nice idioms using channels. Here's one to get us started. In the previous section we launched a sort in the background. A channel can allow the launching goroutine to wait for the sort to complete.

```
c := make(chan int)  // Allocate a channel.
// Start the sort in a goroutine; when it completes, signal on the channel.
go func() {
    list.Sort()
    c <- 1  // Send a signal; value does not matter. 
}()
doSomethingForAWhile()
<-c   // Wait for sort to finish; discard sent value.
```

Receivers always block until there is data to receive. If the channel is unbuffered, the sender blocks until the receiver has received the value. If the channel has a buffer, the sender blocks only until the value has been copied to the buffer; if the buffer is full, this means waiting until some receiver has retrieved a value.

A buffered channel can be used like a semaphore, for instance to limit throughput. In this example, incoming requests are passed to handle, which sends a value into the channel, processes the request, and then receives a value from the channel. The capacity of the channel buffer limits the number of simultaneous calls to process.

```
var sem = make(chan int, MaxOutstanding)

func handle(r *Request) {
    sem <- 1    // Wait for active queue to drain.
    process(r)  // May take a long time.
    <-sem       // Done; enable next request to run.
}

func Serve(queue chan *Request) {
    for {
        req := <-queue
        go handle(req)  // Don't wait for handle to finish.
    }
}
```

Here's the same idea implemented by starting a fixed number of handle goroutines all reading from the request channel. The number of goroutines limits the number of simultaneous calls to process. This Serve function also accepts a channel on which it will be told to exit; after launching the goroutines it blocks receiving from that channel.

```
func handle(queue chan *Request) {
    for r := range queue {
        process(r)
    }
}

func Serve(clientRequests chan *clientRequests, quit chan bool) {
    // Start handlers
    for i := 0; i < MaxOutstanding; i++ {
        go handle(clientRequests)
    }
    <-quit  // Wait to be told to exit.
}
```

### 채널의 채널(Channels of channels) ###
One of the most important properties of Go is that a channel is a first-class value that can be allocated and passed around like any other. A common use of this property is to implement safe, parallel demultiplexing.

In the example in the previous section, handle was an idealized handler for a request but we didn't define the type it was handling. If that type includes a channel on which to reply, each client can provide its own path for the answer. Here's a schematic definition of type Request.

```
type Request struct {
    args        []int
    f           func([]int) int
    resultChan  chan int
}
```

The client provides a function and its arguments, as well as a channel inside the request object on which to receive the answer.

```
func sum(a []int) (s int) {
    for _, v := range a {
        s += v
    }
    return
}

request := &Request{[]int{3, 4, 5}, sum, make(chan int)}
// Send request
clientRequests <- request
// Wait for response.
fmt.Printf("answer: %d\n", <-request.resultChan)
```

On the server side, the handler function is the only thing that changes.

```
func handle(queue chan *Request) {
    for req := range queue {
        req.resultChan <- req.f(req.args)
    }
}
```

There's clearly a lot more to do to make it realistic, but this code is a framework for a rate-limited, parallel, non-blocking RPC system, and there's not a mutex in sight.


### 병렬화 (Parallelization) ###
Another application of these ideas is to parallelize a calculation across multiple CPU cores. If the calculation can be broken into separate pieces, it can be parallelized, with a channel to signal when each piece completes.

Let's say we have an expensive operation to perform on a vector of items, and that the value of the operation on each item is independent, as in this idealized example.

```
type Vector []float64

// Apply the operation to v[i], v[i+1] ... up to v[n-1].
func (v Vector) DoSome(i, n int, u Vector, c chan int) {
    for ; i < n; i++ {
        v[i] += u.Op(v[i])
    }
    c <- 1    // signal that this piece is done
}
```

We launch the pieces independently in a loop, one per CPU. They can complete in any order but it doesn't matter; we just count the completion signals by draining the channel after launching all the goroutines.

```
const NCPU = 4  // number of CPU cores

func (v Vector) DoAll(u Vector) {
    c := make(chan int, NCPU)  // Buffering optional but sensible.
    for i := 0; i < NCPU; i++ {
        go v.DoSome(i*len(v)/NCPU, (i+1)*len(v)/NCPU, u, c)
    }
    // Drain the channel.
    for i := 0; i < NCPU; i++ {
        <-c    // wait for one task to complete
    }
    // All done.
}
```

The current implementation of gc (6g, etc.) will not parallelize this code by default. It dedicates only a single core to user-level processing. An arbitrary number of goroutines can be blocked in system calls, but by default only one can be executing user-level code at any time. It should be smarter and one day it will be smarter, but until it is if you want CPU parallelism you must tell the run-time how many goroutines you want executing code simultaneously. There are two related ways to do this. Either run your job with environment variable GOMAXPROCS set to the number of cores to use (default 1); or import the runtime package and call runtime.GOMAXPROCS(NCPU). Again, this requirement is expected to be retired as the scheduling and run-time improve.

### A leaky buffer ###
The tools of concurrent programming can even make non-concurrent ideas easier to express. Here's an example abstracted from an RPC package. The client goroutine loops receiving data from some source, perhaps a network. To avoid allocating and freeing buffers, it keeps a free list, and uses a buffered channel to represent it. If the channel is empty, a new buffer gets allocated. Once the message buffer is ready, it's sent to the server on serverChan.

```
var freeList = make(chan *Buffer, 100)
var serverChan = make(chan *Buffer)

func client() {
    for {
        var b *Buffer
        // Grab a buffer if available; allocate if not.
        select {
        case b = <-freeList:
            // Got one; nothing more to do.
        default:
            // None free, so allocate a new one.
            b = new(Buffer)
        }
        load(b)              // Read next message from the net.
        serverChan <- b      // Send to server.
    }
}
```

The server loop receives each message from the client, processes it, and returns the buffer to the free list.

```
func server() {
    for {
        b := <-serverChan    // Wait for work.
        process(b)
        // Reuse buffer if there's room.
        select {
        case freeList <- b:
            // Buffer on free list; nothing more to do.
        default:
            // Free list full, just carry on.
        }
    }
}
```

The client attempts to retrieve a buffer from freeList; if none is available, it allocates a fresh one. The server's send to freeList puts b back on the free list unless the list is full, in which case the buffer is dropped on the floor to be reclaimed by the garbage collector. (The default clauses in the select statements execute when no other case is ready, meaning that the selects never block.) This implementation builds a leaky bucket free list in just a few lines, relying on the buffered channel and the garbage collector for bookkeeping.


---

## 에러(Errors) ##
라이브러리 루틴(routines)은, 발생한 에러가 어떤 것인지를 호출자(caller)에게 반드시 알려야 합니다.
이미 언급한 것처럼, Go의 다중 리턴 값(multivalue return) 특성은, 일반적인 처리 결과의 반환 값과 더불어  세부적인 에러 내용을 반환할 수 있게 합니다.
Go에서 에러 타입은 단순히 os.Error 입니다.

```
type Error interface {
    String() string
}
```

라이브러리 작성자는 이 인터페이스를 다른 타입들을 이용하여 자유롭게 구현할 수 있습니다. 이 인터페이스는 에러를 확인할 수 있도록 하며, 또한 부가적인 내용(context)도 제공합니다.

한 가지 예로, os.Open이 반환하는 os.PathError 타입을 보겠습니다.

```
// PathError records an error and the operation and
// file path that caused it.
type PathError struct {
    Op string    // "open", "unlink", etc.
    Path string  // The associated file.
    Error Error  // Returned by the system call.
}

func (e *PathError) String() string {
    return e.Op + " " + e.Path + ": " + e.Error.String()
}
```

PathError의 String 함수는 아래와 같은 문자열을 반환합니다.
```
open /etc/passwx: no such file or directory
```

에러가 발생한 작업(operation), 파일 이름, 시스템 에러를 같이 표시하는 것은, 단순히 'no such file of directory'라는 메시지 보다 더 많은 정보를 제공합니다.

에러의 세부 내용을 정확히 알고 있는 호출 함수(Callers)는 에러 타입을 바꾸거나 특정 에러에 대해 assertion을 발생시키거나 또는 상세 메시지를 뽑아낼 수 있습니다.

PathError의 경우, 내부 에러(internal Error) 코드를 체크해서 특정 에러 발생시에 이를 복구할 수 있습니다.

```
for try := 0; try < 2; try++ {
    file, err = os.Open(filename, os.O_RDONLY, 0)
    if err == nil {
        return
    }
    if e, ok := err.(*os.PathError); ok && e.Error == os.ENOSPC {
        deleteTempFiles()  // Recover some space.
        continue
    }
    return
}
```

### 패닉(Panic) ###
호출자(caller)에게 에러를 보고하는 전형적인 방법은 os.Error를 반환하는 것입니다. Read() 함수는 읽어 들인 byte 수와 os.Error를 반환 값으로 사용하기 때문에, 위에서 언급한 방법을 사용하는 좋은 예시 입니다. 그런데, 만약 어떤 에러가 복구할 수 없는 것이라면 어떻게 해야 할까요? 대부분의 경우 그 프로그램은 계속 진행될 수 없어야 합니다.

이를 위해 Go에는 panic이라는 내장 함수(built-in function)가 존재합니다. panic 함수는 런타임 에러(run-time error)를 만들어 해당 프로그램을 중지시킵니다.(다음 섹션인, 복구(Recover) 섹션을 참고하세요.)
이 함수는 임의의 1개의 인자를 받는데, 보통 프로그램이 중지된 원인이 들어있는 string 입니다. 또한, 이는 무한 루프를 빠져 나오는 것처럼, 어떤 불가능한 일이 발생했음을 표시할 수 있는 방법이기도 합니다. 실제로, 컴파일러는 함수 마지막의 panic()을 인식하고, 반환 구문을 위한 검사(check)를 하지 않습니다.

```
// A toy implementation of cube root using Newton's method.
func CubeRoot(x float64) float64 {
    z := x/3   // Arbitrary intitial value
    for i := 0; i < 1e6; i++ {
        prevz := z
        z -= (z*z*z-x) / (3*z*z)
        if veryClose(z, prevz) {
            return z
        }
    }
    // A million iterations has not converged; something is wrong.
    panic(fmt.Sprintf("CubeRoot(%g) did not converge", x))
}
```

위 코드는 하나의 예시입니다. 실제 라이브러리 함수들은 panic()을 회피해야 합니다.
만약 발생한 문제가 해결 가능하거나 프로그램의 진행에 무방한 것이라면, 전체 프로그램을 중지시키는 것보다는 계속 진행시키는 것이 더 좋습니다.
대표적인 사례는 초기화 부분입니다.
만약 설정 값을 읽을 수 없어서 프로그램을 진행할 수 없다면, panic()을 사용할 수 있는 충분한 이유가 될 수 있습니다.

```
var user = os.Getenv("USER")

func init() {
    if user == "" {
        panic("no value for $USER")
    }
}
```

### 복구(Recover) ###
When panic is called, including implicitly for run-time errors such as indexing an array out of bounds or failing a type assertion, it immediately stops execution of the current function and begins unwinding the stack of the goroutine, running any deferred functions along the way. If that unwinding reaches the top of the goroutine's stack, the program dies. However, it is possible to use the built-in function recover to regain control of the goroutine and resume normal execution.

A call to recover stops the unwinding and returns the argument passed to panic. Because the only code that runs while unwinding is inside deferred functions, recover is only useful inside deferred functions.

One application of recover is to shut down a failing goroutine inside a server without killing the other executing goroutines.

```
func server(workChan <-chan *Work) {
    for work := range workChan {
        go safelyDo(work)
    }
}

func safelyDo(work *Work) {
    defer func() {
        if err := recover(); err != nil {
            log.Println("work failed:", err)
        }
    }()
    do(work)
}
```

In this example, if do(work) panics, the result will be logged and the goroutine will exit cleanly without disturbing the others. There's no need to do anything else in the deferred closure; calling recover handles the condition completely.

Because recover always returns nil unless called directly from a deferred function, deferred code can call library routines that themselves use panic and recover without failing. As an example, the deferred function in safelyDo might call a logging function before calling recover, and that logging code would run unaffected by the panicking state.

With our recovery pattern in place, the do function (and anything it calls) can get out of any bad situation cleanly by calling panic. We can use that idea to simplify error handling in complex software. Let's look at an idealized excerpt from the regexp package, which reports parsing errors by calling panic with a local Error type. Here's the definition of Error, an error method, and the Compile function.

```
// Error is the type of a parse error; it satisfies os.Error.
type Error string
func (e Error) String() string {
    return string(e)
}

// error is a method of *Regexp that reports parsing errors by
// panicking with an Error.
func (regexp *Regexp) error(err string) {
    panic(Error(err))
}

// Compile returns a parsed representation of the regular expression.
func Compile(str string) (regexp *Regexp, err os.Error) {
    regexp = new(Regexp)
    // doParse will panic if there is a parse error.
    defer func() {
        if e := recover(); e != nil {
            regexp = nil    // Clear return value.
            err = e.(Error) // Will re-panic if not a parse error.
        }
    }()
    return regexp.doParse(str), nil
}
```

If doParse panics, the recovery block will set the return value to nil—deferred functions can modify named return values. It then will then check, in the assignment to err, that the problem was a parse error by asserting that it has type Error. If it does not, the type assertion will fail, causing a run-time error that continues the stack unwinding as though nothing had interrupted it. This check means that if something unexpected happens, such as an array index out of bounds, the code will fail even though we are using panic and recover to handle user-triggered errors.

With error handling in place, the error method makes it easy to report parse errors without worrying about unwinding the parse stack by hand.

Useful though this pattern is, it should be used only within a package. Parse turns its internal panic calls into os.Error values; it does not expose panics to its client. That is a good rule to follow.

By the way, this re-panic idiom changes the panic value if an actual error occurs. However, both the original and new failures will be presented in the crash report, so the root cause of the problem will still be visible. Thus this simple re-panic approach is usually sufficient—it's a crash after all—but if you want to display only the original value, you can write a little more code to filter unexpected problems and re-panic with the original error. That's left as an exercise for the reader.


---

## 웹서버 (A web server) ##
Go를 이용해 구현한 웹 서버를 살펴보는 것으로 마무리 하겠습니다. 이 프로그램은 실제 web re-server중의 하나입니다.
구글은 http://chart.apis.google.com에서 데이터를 차트와 그래프 형태로 자동 변환하는 서비스를 제공하고 있는데, 이 서비스를 이용하는 query문을 만들기 위해서는, 사용자가 데이터를 url에 직접 추가시켜줘야 하기 때문에 불편한 면이 있습니다.

이 프로그램은 이러한 문제를 해결하기 위해, chart 서버에 QR 코드를 생성하도록 함으로써 편리한 방법을 제공합니다.
QR 코드는 사용자의 핸드폰 카메라에 담을 수 있고 URL로 해석되므로, 핸드폰의 작은 키보드로 URL을 입력해야 하는 수고를 덜 수 있습니다.

아래에 완성된 코드와 설명이 있습니다.
```
package main

import (
    "flag"
    "http"
    "io"
    "log"
    "template"
)

var addr = flag.String("addr", ":1718", "http service address") // Q=17, R=18
var fmap = template.FormatterMap{
    "html": template.HTMLFormatter,
    "url+html": UrlHtmlFormatter,
}
var templ = template.MustParse(templateStr, fmap)

func main() {
    flag.Parse()
    http.Handle("/", http.HandlerFunc(QR))
    err := http.ListenAndServe(*addr, nil)
    if err != nil {
        log.Fatal("ListenAndServe:", err)
    }
}

func QR(w http.ResponseWriter, req *http.Request) {
    templ.Execute(w, req.FormValue("s"))
}

func UrlHtmlFormatter(w io.Writer, fmt string, v ...interface{}) {
    template.HTMLEscape(w, []byte(http.URLEscape(v[0].(string))))
}


const templateStr = `
<html>
<head>
<title>QR Link Generator</title>
</head>
<body>
{.section @}
<img src="http://chart.apis.google.com/chart?chs=300x300&cht=qr&choe=UTF-8&chl={@|url+html}"
/>
<br>
{@|html}
<br>
<br>
{.end}
<form action="/" name=f method="GET"><input maxLength=1024 size=70
name=s value="" title="Text to QR Encode"><input type=submit
value="Show QR" name=qr>
</form>
</body>
</html>
`
```

main 까지의 코드들은 쉽게 이해할 수 있어야 합니다. 먼저,  서버에서 사용할 기본 HTTP 포트로 flag를 셋팅합니다.
여기서, 템플릿 변수 templ은 흥미로운 동작을 발생시킵니다.
templ은, 웹 서버가 페이지를 표시할 때 실행되어지는 HTML 템플릿을 빌드합니다. 이것에 관해 좀 더 살펴보겠습니다.

main 함수는 flag를 해석하고 위에 언급한 매커니즘을 사용하는 QR 함수를 서버의 root path에 바인딩 합니다.그런 다음, http.ListenAndServe가 서버 시작을 위해 호출되며, 서버가 동작하는 동안 block됩니다.

QR 함수는 단순히 form 데이터에 포함되어 있는 요청을 받고 s로 명칭된 form value내의 데이터에 대해 template을 실행합니다.
json 포맷을 사용하는 템플릿 패키지는 강력한 기능들을 가지고 있습니다. 이 프로그램은 단지 그러한 기능들의 하나를 보여줍니다.
핵심은, templ에 넘겨진 data 아이템들로부터 파생된 elements을 치환함으로써, HTML 코드를 곧바로 다시 쓴다는 것입니다.
위의 코드에서, templ.Execute()가 바로 이 부분입니다.


템플릿 텍스트(templateStr)내에서, 중괄호로 분리된 조각들은 템플릿 actions을 나타냅니다.
{.section @} 부터 {.end} 까지의 코드는 data item @를 실행합니다. @는 '현재 아이템'을 의미하는 form value를 나타냅니다.(string이 비어있다면 이 템플릿 코드들은 실행되지 않습니다.)

{@|url+html}은, "url+html" 이름 옆의 formatter map(fmap)내에 설치된 formatter를 이용하여 데이터를 실행하는 것을 의미합니다.
즉, UrlHtmlFormatter 함수는 웹 페이지에 string을 안전하게 표시하도록 합니다.
템플릿 스트링의 나머지는 페이지가 로딩될 때, 단순히 HTML을 표시하도록 합니다.

만약 지금까지의 설명이 부족하다고 생각하시면, 더 폭 넓은 이해를 위해 template package 문서를 참고하시기 바랍니다.

몇 줄의 코드와 데이터 지향(data-driven) HTML 텍스트로 여러분들은 쓸만한 웹서버를 가지게 되었습니다.

Go는 몇 줄의 코드만으로 많은 것을 해낼 수 있을 만큼 강력합니다.

---
