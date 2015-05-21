# 요약 #
  * 내용 : Go언어 입문자를 위한 설명서
  * 원문 : [A Tutorial for the Go Programming Language](http://golang.org/doc/go_tutorial.html)
  * 번역자 : yongsung.kang@gmail.com




# Go언어 입문자를 위한 설명서 #
## 소개 ##
이 문서는 Go 프로그래밍 언어의 기본을 소개하기 위한 자습서이며, C 혹은 C++ 에 익숙한 프로그래머들을 대상으로 작성되었습니다. 이 문서는 Go언어에 대한 포괄적인 안내문은 아닙니다; 포괄적인 안내문 성격의 문서는 언어규격 문서를 보시면 됩니다. 이 자습서를 읽은 후 반드시 효과적인 Go 문서를 보시길 바랍니다. 효과적인 Go 문서는 Go로 프로그래밍을 할때 사용되는 형식과 관용구에 대해 좀 더 깊이 다루고 있습니다. 또한, Go에 대한 3일 일정의 발표자료 형식으로 된 자료도 있습니다. 매우 오래된 자료이긴 하지만, 몇가지 배경지식과 많은 예제로 구성되어 있습니다. 첫날, 둘째날, 세쩻날.

이 문서의 내용은 Go언어의 핵심 특징들을 잘 나타낼 수 있는 일반적인 프로그램들을 통해 진행됩니다. 모든 프로그램들은 (작성 시점에서) 잘 동작하고 검토되었으며, 다음의 디렉토리 (/doc/progs/) 에 저장되어 있습니다.

프로그램 상자의 각 줄 앞에는 원본 파일에 있는 줄번호가 주석으로 되어 있습니다; 좀더 깔끔하게 보이기 위해 빈줄은 줄번호를 넣지 않았습니다.


## Hello, World ##
많이 쓰이는 방법으로 시작해 봅시다:

```
05    package main

07    import fmt "fmt"  // 형식화 된 입출력 함수가 구현된 패키지.


09    func main() {
10        fmt.Printf("Hello, world; or Καλημέρα κόσμε; or こんにちは 世界\n")
11    }
```

모든 Go 소스 파일은 package 문을 이용해 자신의 속한 패키지를 선언한다. 또한, 다른 패키지에 있는 것들을 사용하기 위해 다른 패키지를 import 할 수 있습니다. 이 프로그램은 우리에게 익숙하지만 패키지화 되어 있고, 첫글자가 대문자로 바뀐 fmt.Printf 에 접근할 수 있도록 fmt 패키지를 포함시켰습니다.

함수들은 func 키워드를 이용해 나타냅니다. main 패키지의 main 함수는 프로그램의 동작이 시작하는 곳 입니다(초기화 과정 이후에).

String 정수는 UTF-8로 인코딩된 Unicode 문자들을 포함할 수 있습니다. (사실, Go 소스 파일들은 UTF-8로 인코딩 되도록 정의 되어 있습니다.)

주석표기는 C++과 같습니다.

```
    /* ... */
    // ...
```

출력에 대해서는 차후에 좀더 알아볼 예정 입니다.

## 세미콜론 ##

여러분은 어쩌면 우리 프로그램에 세미콜론이 없다는 것을 알아 채셨을 지도 모르겠습니다. Go 코드에는 for 반복문의 각 항을 구분하기 위해 전형적으로 쓰인 세미콜론 밖에는 보이지 않습니다. 모든 문장의 끝에 세미콜론을 넣을 필요가 없습니다.

사실, C 나 Java 같은 규칙적인 언어는 세미콜론을 사용하지만, 문장의 끝을 나타내기 위해 자동으로 문장의 끝에 삽입되고 있습니다. 여러분이 스스로 이런 일을 할 필요는 없습니다.

이런 일들이 어떻게 이루어지는지 좀더 자세히 알고 싶다면 언어 규격 문서를 보시면 되지만, 실제로 여러분께서 아셔야 하는 것은 한줄의 끝에 세미콜론을 넣을 필요가 없다는 것 입니다. (한 줄에 여러 문장을 넣기를 원하신다면 세미콜론을 써야 합니다.) 추가적으로, 닫기 중괄호(closing brace)앞에 세미콜론은 필요 없습니다.

이런 방식은 코드를 깔끔하게 보이게 만듭니다. 한가지 알아야 할 것은 if 문 같은 문장 구조에서는 열기 중괄호(opening brace)를 그 if 문과 같은 라인에 넣어야 한다는 것 입니다; 만일 그렇게 하지 않는다면, 컴파일이 되지 않거나 잘못된 결과를 낼 것 입니다. Go 언어는 중괄호 스타일에 대해 어느 정도 강제하고 있습니다.

## 컴파일 ##

Go 언어는 컴파일 언어 입니다. 현재 두개의 컴파일러가 있습니다. Gcco는 GCC를 이용하는 컴파일러 입니다. 또한, 각각의 시스템 구조에 따른 서로 다른(그리고 이상한)이름을 가진 컴파일러들의 조합도 있습니다: 64-bit x86을 위한 6g, 32-bit x86을 위한 8g 등등. 이러한 컴파일러들은 gccgo 보다 매우 빠르게 동작하지만 gccgo보다 덜 효율적인 코드를 생성해 냅니다. 또한, (2009년 이글을 쓰는 시점에)이런 컴파일러들은 매우 강인한 실시간 시스템들을 더 많이 가지고 있으며, gccgo도 따라잡는 중 입니다.

6g로 우리 프로그램을 어떻게 컴파일 하고 실행하는지 보면 다음과 같습니다.

```
    $ 6g helloworld.go  # 컴파일; helloworld.6 로 변환됩니다
    $ 6l helloworld.6   # 링크; 6.out
    $ 6.out
    Hello, world; or Καλημ?ρα κ?σμε; or こんにちは 世界
    $
```

gccgo로는 좀 더 전통적으로 보입니다.

```
    $ gccgo helloworld.go
    $ a.out
    Hello, world; or Καλημ?ρα κ?σμε; or こんにちは 世界
    $
```

## 에코 ##

다음은 Unix 유틸리티 echo(1) 버전 입니다:

```


05    package main

07    import (
08        "os"
09        "flag"  // 커멘드 라인 옵션 파서
10    )


12    var omitNewline = flag.Bool("n", false, "don't print final newline")


14    const (
15        Space = " "
16        Newline = "\n"
17    )


19    func main() {
20        flag.Parse()   // arg list를 확인 해서 flags를 설정합니다.
21        var s string = ""
22        for i := 0; i < flag.NArg(); i++ {
23            if i > 0 {
24                s += Space
25            }
26            s += flag.Arg(i)
27        }
28        if !*omitNewline {
29            s += Newline
30        }
31        os.Stdout.WriteString(s)
32    }


```

이 프로그램은 작지만 몇가지 새로운 일들을 수행 합니다. 바로 이전 예제에서, 함수를 소개하는 func 문이 있었습니다. 키워드 var, const, type(아직 사용되지는 않았습니다)들 또한 import문 처럼 선언을 나타냅니다. 7-10, 14-17 줄을 보시면, 같은 종류의 선언문들을 그룹화 해서 한줄에 한개의 아이템씩 리스트에 모아 넣을 수 있습니다. 꼭 이렇게 해야할 필요는 없습니다; 다음과 같이 해도 됩니다.

```
    const Space = " "
    const Newline = "\n"
```

이 프로그램은 `*os.File type`인 Stdout 변수에 접근하기 위해 "os" 패키지를 가져옵니다(import). import 문은 실제로는 선언 입니다: 위에 있는 'Hello World' 프로그램에서 사용된 것 처럼 일반적인 형식은, 현재 디렉토리 혹은 표준위치에 있는 디렉토리에 있는 "fmt" 파일에서 가져온(import) 패키지의 멤버에 접근할 수 있도록 식별자 (fmt)가 사용됩니다. (즉, 처음 나오는 fmt는 import된 패키지의 멤버에 접근하는데 쓰이고, 두번째 "fmt"는 import 하는 파일을 나타내고 있습니다) 하지만, 이 프로그램에서는 import문에 분명한 이름이 누락되어 있습니다; 기본적으로 패키지들은 import된 패키지에 의해 정의된 이름(관습적으로 자신의 파일명)을 사용해서 import됩니다. 우리의 "Hello World" 프로그램은 단순히 import "fmt"를 사용했습니다.

여러분은 여러분의 고유의 import 명칭을 지정할 수 있지만, 이름이 곂치는 문제가 있을 때 외에는 그럴 필요가 없습니다.

os.Stdout을 쓸 수 있는 상황이므로, 우리는 문자열을 프린트 하기 위해 WriteString 메소드도 사용할 수 있습니다.

flag 패키지를 import하고, 12번째 줄에서 echo의 -n flag의 값을 가질 전역변수를 만들었습니다. 이 변수 omitNewline은 bool형 포인터 입니다.

main.main에서, 우리는 인자들을 파싱하고(20번째 줄) 그후, 출력하는데 사용할 문자열 지역 변수를 만듭니다.

선언문은 다음과 같은 형식입니다.

```
    var s string = ""
```

선언문은 var 키워드, 변수명, 타입, 등호, 변수의 초기값 순으로 이루어 집니다.

Go언어는 간결한 형식이 되려고 하기 때문에, 이 선언문은 짭게 바뀔 수 있습니다. 문자열 상수는 문자열 형이므로, 우리는 컴파일러에게 알릴 필요는 없습니다. 따라서 다음과 같이 쓸 수 있습니다.

```
    var s = ""
```

심지어, 관용구를 사용해 더 짧게 쓸 수 있습니다.

```
    s := ""
```

:= 연산자는 Go언어에서 초기 선언을 나타내기 위해 많이 사용됩니다. 다음 줄에 있는 for문에도 사용됩니다.

```
22        for i := 0; i < flag.NArg(); i++ {
```

flag 패키지는 반복적으로 추출해서 매개 변수를 파싱하고 flag가 아닌 매개변수들(non-flag arguments)를 리스트에 남겨 놓습니다.

Go 언어의 for문은 C언어와 몇가지 면에서 다른점이 있습니다. 첫째, for문이 유일한 반복 구조 입니다; while이나 do같은 문이 없습니다. 둘째, 문장에 괄호가 없습니다. 하지만, 몸체에는 필수적으로 중괄호가 필요합니다. 이런 특성은 if문과 swith문에도 동일하게 적용됩니다. 차후의 예제들에서 for문이 다른식으로 쓰여지는 것을 보여드릴 것 입니다.

반복문의 몸체는 +=를 사용하여 매개변수와 이를 구분하는 공백을 추가하면서 문자열 s를 만듭니다. 반복문 이후에, 만일 -n flag가 설정되어 있지 않으면, 프로그램은 새로운 줄을 추가 합니다. 마지막으로, 결과를 출력 합니다.

main.main은 반환형이나 매개변수가 없는 함수라는 것을 기억 하시기 바랍니다. main.main의 마지막까지 실행되는 것은 "성공"을 의미합니다; 만일 여러분이 에러를 반환하고 싶다면, 다음 문구를 호출 하시면 됩니다.

```
    os.Exit(1)
```

os 패키지는 프로그램을 시작하기위한 다른 필수 요소들을 포함하고 있습니다; 예를 들자면, os.Args는 명령어쉘 명렁어 매개변수에 접근하기 위한 flag 패키지에 의해 부분적으로 이용되고 있습니다.

## 형(Type)에 관한 간략한 설명 ##
Go 언어는 int, uint(unsigned int)와 같은 몇개의 익숙한 형(type)들을 가지고 있습니다. -이러한 형(type)들은 작동하는 기계를 위한 "적절한" 크기의 값을 나타냅니다. 형(type)들은 int8, float64 들 처럼 명확한 크기를 정의하기도 합니다. 이런 형(type)들은 명확한 형(type)들 입니다; 심지어 int와 int32 둘 다 모두 32bit 크기를 가지고 있어도 둘은 같은 형(type)이 아닙니다. uint8의 동의어로 문자열을 위한 구성요소 형(element type)인 byte가 있습니다.

부동소수점형들은 항상 크기가 있습니다: float32, float64, complex64(두개의 float32), complex128(두개의 float64). 복소수(Complex numbers)들은 이 자습서의 요점에서 벗어 납니다.

string역시 기본적으로 들어 있는 형(type)입니다. 문자열들은 변경되지 않는 값들 입니다 - 그냥 byte값들의 열이 아닙니다. 여러분이 한번 문자열 값을 설절하면, 바꿀 수 없습니다. 물론, 다시 변수에 값을 넣으면 문자열을 바꿀 수 있습니다. 다음에 나오는 strings.go 의 일부분은 유효한 코드입니다.:

```
 
10        s := "hello"
11        if s[1] != 'e' { os.Exit(1) }
12        s = "good bye"
13        var p *string = &s
14        *p = "ciao"

```

하지만 다음에 나오는 문장들은 string 형의 값을 변경하려고 하기 때문에 유효하지 않습니다.

```
    s[0] = 'x'
    (*p)[1] = 'y'
```

Go 언어의 문자열들은 약간 C++에서의 const strings와 닮아 있고, 문자열 포인터는 const string 참조와 유사합니다.

맞습니다. 이것들은 포인터 입니다. 하지만, Go언어는 포인터 사용법을 조금 단순하게 만들었습니다; 계속 읽어 보시기 바랍니다.

배열들은 다음과 같이 선언됩니다:

```
    var arrayOfInt [10]int
```

배열은 문자열처럼 값의 열 이지만 변경이 가능합니다. C에서는 arrayOfInt가 int를 가르키는 포인터로 사용될 수 있는 것과는 다르게, Go에서는 배열들이 값이므로 배열에 대한 포인터에 대해 언급하는 것은 중요하고 유용합니다.

배열의 크기는 배열의 형의 일부입니다; 하지만, 같은 요소 형을 가지고 있다면, 어떤배열 이나 어떤 크기를 참조하기 위해 slice 변수를 선언 할 수 있습니다. slice 표현은 `a[low:high]`형식을 가진 문구 이며, low 부터 high-1 까지의 배열 인덱스를 나타냅니다; 결과적으로 slice는 0부터 high-low-1 로 인덱스 되게 됩니다. 짧게 말해서, slice는 배열과 매우 비슷하지만 정확한 크기가 없습니다(`[]`와 `[10]`을 비교해 보시기 바랍니다) 그리고, slice는 어느 정규 배열의 일부던지 참조할 수 있습니다. 여러개의 slice가 만일 같은 배열의 일부를 나타낸다면 데이터를 공유할 수 있습니다; 여러 배열들이 데어터를 공유하지는 못합니다.

Go 프로그램에서는 배열보다 slice가 더 일반적으로 사용됩니다; slice는 더 유연하고, 의미를 참조하고, 효과적 입니다. 정규 배열의 저장 공간 배치의 정교한 조절이 부족한 부분 입니다; 만일 여러분이 여러분의 구조체안에 백개의 요소를 가진 배열이 저장되어 있다면, 여러분은 정규 배열을 사용해야 합니다. Slice를 생성하기 위해 복합 생성자를 사용해야 합니다 - 다음 예처럼, 형(type), 중괄호 묶음으로 이루어 집니다.

```
    [3]int{1,2,3}
```

이 경우 생성자는 3개의 정수형 배열을 만들었습니다.

배열을 함수에 전달할 때, 여러분은 거의 slice로 변경된 매개변수를 전달하길 원할 것 입니다. 여러분이 함수를 호출할 때, 전달하려는 배열을 참조하는 slice를 만들어서 전달 합니다. 기본적으로, slice는 참조하려는 배열의 하한과 상한값과 같으므로, 모든 배열을 나타내는 `[:]` 을 사용합니다.

slice를 이용해 다음과 같은 함수를 작성 할 수 있습니다(sum.go로의 일부).

```

09    func sum(a []int) int { // 정수형(int) 반환
10        s := 0
11        for i := 0; i < len(a); i++ {
12            s += a[i]
13        }
14        return s
15    }

```

sum()함수에서 반환형 int가 매개변수 리스트 다음에 정의되는 점을 기억하시기 바랍니다.
함수를 호출하기 위해서 배열을 자릅니다(slice). 이 복잡한 호출은(이후에 좀 더 같단한 방법을 보여드리겠습니다.) 배열을 구성하고 자릅니다(slice).

```
    s := sum([3]int{1,2,3}[:])
```

만일 여러분이 정규 배열을 생성하였으나 요소들의 갯수를 컴파일러에 알릴 필요가 없다면, 배열 크기를 ...으로 하시면 됩니다.

```
    s := sum([...]int{1,2,3}[:])
```

하지만, 이건 필요이상 복잡합니다. 실제적으로 데이터 구조안에 저장공간 구조에 관해 꼼꼼하게 신경쓸 필요가 없다면 slice 자체 -즉, 크기를 자지지 않는 빈 괄호를 사용한 - 만으로도 충분합니다:

```
    s := sum([]int{1,2,3})
```

map 이라는 것도 있는데, 다음과 같이 초기화 시키면 됩니다.

```
    m := map[string]int{"one":1 , "two":2}
```

요소들의 갯수를 반환하는 내장된 함수 len()은 sum에서 처음 선보였습니다. 이 함수는 문자열, 배열, slice, map, channel 에서 동작 합니다.

덧붙여, 문자열, 배열, slice, map, channel에서 동작하는 함수가 또 있는데 그건 for 반복문에 쓰이는 range 입니다. 다음처럼 쓰는 대신

```
    for i := 0; i < len(a); i++ { ... }
```

slice나 map이나 등등 의 요소들을 반복해서 무언가를 할 때, 다음과 같이 쓸 수 있습니다.

```
    for i, v := range a { ... }
```

여기서 i는 인덱스 이고, v는 대상의 범위안에 있는 연속된 요소들의 값을 나타냅니다. 좀 더 많은 예제를 보시려면, 효과적인 Go 문서를 보시기 바랍니다.

## 할당(Allocation)에 대한 간략한 설명 ##

Go언어의 대부분의 형(type)은 값입니다. 만일 여러분이 int 나 struct 나 배열을 가지고 있다면, 할당은 객체의 내용을 복사 하는 것 입니다. 새로운 변수를 할당 하기 위해서는 new() 함수를 사용하시기 바랍니다. new()함수는 할당된 자장공간의 포인터를 반환 합니다.

```
    type T struct { a, b int }
    var t *T = new(T)
```

또는 관용구를 사용해서,

```
    t := new(T)
```

map, slice, channel들 같은 형들은 문장(semantics)을 참조합니다. 만일 여러분이 slice나 map을 가지고 있고 그 내용을 변경한다면, 같은 위치에 놓은 데이터를 참조하는 다른 변수들 역시 변경될 것 입니다. 이 세가지 형에 대해서는 make()를 사용합니다:

```
    m := make(map[string]int)
```

이 문장은 내용을 저장할 준비가 되도록 새로운 map을 초기화 합니다. 만일 단순히 map을 선언만 하고 싶다면 다음과 같이 하면 됩니다.

```
    var m map[string]int
```

위 문장은 아무것도 가질 수 없는 nil 참조를 생성 했습니다. map을 사용하기 위해서는 make()를 사용해 참조를 초기화 하거나 기존에 있는 map으로 할당 해야 합니다.

make(T)는 T형을 반환하지만, new(T)는 ```*T```형을 반환 한다는 것을 기억 하시기 바랍니다. 만일 여러분이 (실수로) new()로 객체 참조를 할당하면, nil 참조를 하는 포인터를 반환 받게 됩니다. 이것은 마치 초기화 되지 않는 변수를 선언하고 그 주소를 가져오는 것과 같습니다.
## 상수(Constants)에 대한 간략한 설명 ##
Go에서 정수는 다양한 크기(size)를 가지지만 정수 상수(integer constants)는 그렇지 않습니다. 0LL이나 0x0UL과 같은 상수는 없습니다. 대신에 정수 상수는 정밀도가 낮은 정수 변수에 값을 할당될 때만은 오버플로가 발생하는 더 높은 정확도의 값이 됩니다.
```
const hardEight = (1 << 100) >> 97  // legal
```
언어의 스펙이 난해하다고 할 수 있겠지만 이해하기 쉬운 예제를 봅시다.
```
    var a uint64 = 0  // a has type uint64, value 0
    a := uint64(0)    // equivalent; uses a "conversion"
    i := 0x1234       // i gets default type: int
    var j int = 1e6   // legal - 1000000 is representable in an int
    x := 1.5          // a float64, the default type for floating constants
    i3div2 := 3/2     // integer division - result is 1
    f3div2 := 3./2.   // floating-point division - result is 1.5
```
변환은 ints끼리의 단순한 경우와 정수와 부동소수점 사이의 변환에만 동작합니다. Go에서는 자동으로 수를 변환하지 않습니다. 상수를 변수에 할당하는 시점에 정확한 크기와 타입을 가집니다.
## I/O 패키지 ##
다음으로 open/close/read/write 인터페이스를 가지고 file I/O를 담당하는 간단한 패키지를 봅시다. 다음은, file.go의 시작부분 입니다.
```
05    package file

07    import (
08        "os"
09        "syscall"
10    )


12    type File struct {
13        fd   int    // file descriptor number
14        name string // file name at Open time
15    }
```
처음 몇 줄은 패키지 이름으로 'file'을 선언하고 2개 패키지를 import합니다. os패키지는 file에 대해서 동일한 관점을 가지도록 여러 운영체제들 사이에서의 차이점을 감춥니다. 여기서는 에러처리 유틸리티를 사용하며 파일 I/O의 기초를 새로 생성해봅니다.


다른 패키지인 syscall은 기본 운영 시스템의 호출에 대한 기본적인 인터페이스를 제공합니다.

다음으로 타입 정의가 있습니다. type 키워드로 특정 type 선언을 하며 이 경우 File이라는 자료구조가 됩니다. 좀더 재밌게 하기 위해 우리 File은 file descriptor가 참조하는 파일의 이름을 포함합니다.

File은 대문자로 시작하는데 이렇게 하면 패키지 사용자가 패키지의 외부에 사용이 가능한 type이 됩니다. Go에서 정보의 가시성에 관한 원칙은 간단합니다. 만약 최상위 type, 함수, method, 상수, 변수 혹은 구조체의 필드나 method의 이름이 대문자로 시작하면 패키지의 사용자는 접근이 가능합니다.
반면에 소문자일 경우에는 패키지 내부에서만 접근이 가능합니다. 이는 단순한 규약 이상입니다. 이런 원칙은 컴파일러에서 강제하는 사항입니다. Go에서 외부에 공개한 이름에 대한 용어를 "exported"라 합니다.

File의 경우에서 모든 field가 소문자라서 사용자가 참조할 수 없지만 조만간 일부를 대문자로 만들어서 'exported'시킬 것 입니다.

먼저 File을 만드는 factory 함수를 봅시다.
```
17    func newFile(fd int, name string) *File {
18        if fd < 0 {
19            return nil
20        }
21        return &File{fd, name}
22    }
```


## Rotting cats ##
file 패키지를 만드는데 있어 유닉스의 유틸리티인 cat(1)의 간단한 버전인 progs/cat.go를 봅시다.
```
05    package main

07    import (
08        "./file"
09        "flag"
10        "fmt"
11        "os"
12    )


14    func cat(f *file.File) {
15        const NBUF = 512
16        var buf [NBUF]byte
17        for {
18            switch nr, er := f.Read(buf[:]); true {
19            case nr < 0:
20                fmt.Fprintf(os.Stderr, "cat: error reading from %s: %s\n", f.String(), er.String())
21                os.Exit(1)
22            case nr == 0: // EOF
23                return
24            case nr > 0:
25                if nw, ew := file.Stdout.Write(buf[0:nr]); nw != nr {
26                    fmt.Fprintf(os.Stderr, "cat: error writing from %s: %s\n", f.String(), ew.String())
27                    os.Exit(1)
28                }
29            }
30        }
31    }


33    func main() {
34        flag.Parse() // Scans the arg list and sets up flags
35        if flag.NArg() == 0 {
36            cat(file.Stdin)
37        }
38        for i := 0; i < flag.NArg(); i++ {
39            f, err := file.Open(flag.Arg(i))
40            if f == nil {
41                fmt.Fprintf(os.Stderr, "cat: can't open %s: error %s\n", flag.Arg(i), err)
42                os.Exit(1)
43            }
44            cat(f)
45            f.Close()
46        }
47    }
```
지금까지 따라하기 쉬었지만 switch문은 일부 새로운 특징을 도입 했습니다. for 루프와 같이 if나 switch는 초기화 부분을 포함할 수 있습니다. 18번째 줄에서 switch는 f.Read()에서 변수를 반환하기 위해 nr과 er 변수를 생성하기 위해 사용합니다. 25번째 줄 if도 동일합니다. switch문은 일반적 입니다. 처음부터 마지막까지 처음으로 값을 만족하는 경우를 찾습니다. case 표현은 동일한 type이라면 구태여 상수나 정수일 필요는 없습니다.

switch 값이 단순히 true라면 for 문에서와 같이 생략가능하며 값을 생략하면 true를 의미합니다. 사실 이런 switch는 if-else 체인의 형태입니다. 여기서 switch 문에서 각 case는 묵시적인 break를 가집니다.

25번째 줄은 incoming buffer를 slicing함으로써 Write()를 호출합니다. 자체가 slice 입니다. slice는 I/O buffer를 처리하는 표준 Go 방식을 제공합니다.

이제 입력으로 옵션으로 rot13을 처리하는 cat의 변형을 만들어 보겠습니다. 단지 byte들을 처리하는 것은 쉽지만 interface의 Go 개념을 활용해 보도록 하겠습니다.

cat() 서브루틴은 f:의 2개 method인 Read()와 String()만 사용 합니다. 따라서 정확히 이 2개 method를 가지는 interface를 정의하는 것으로 시작해 봅시다. 여기 progs/cat\_rot13.go 코드가 있습니다.

```
26    type reader interface {
27        Read(b []byte) (ret int, err os.Error)
28        String() string
29    }
```

reader의 2개 method를 가지는 특정 type이 interface를 구현한다고 합시다. 따라서 file.File은 이 method들을 구현하기 때문에 reader interface를 구현합니다.  우리는   `*file.File` 대신 reader를 받아들일 수 있도록 cat 서브루틴을 약간 고칠 수 있고 그렇게 하면 잘 동작 하지만, 이미 있는 reader를 싸고 있고, data에 rot13을 적용하는 reader를 구현 하도록 두번째 타입을 써서 약간 꾸며보도록 합시다. 이렇게 하기 위해, 우리는 type을 정의하고 메소드를 구현하면 reader 인터페이스의 두번째 구현이 완성 됩니다.

```

31    type rotate13 struct {
32        source reader
33    }

35    func newRotate13(source reader) *rotate13 {
36        return &rotate13{source}
37    }


39    func (r13 *rotate13) Read(b []byte) (ret int, err os.Error) {
40        r, e := r13.source.Read(b)
41        for i := 0; i < r; i++ {
42            b[i] = rot13(b[i])
43        }
44        return r, e
45    }


47    func (r13 *rotate13) String() string {
48        return r13.source.String()
49    }
50    // end of rotate13 implementation
```

(42줄에서 호출된 rot13 함수는 중요한것이 아니므로 여기서 다시 언급하지 않겠습니다.)

새로운 기능을 사용하기 위해서 flag를 정의 합니다:

```
14    var rot13Flag = flag.Bool("rot13", false, "rot13 the input")
```

그리고, 거의 안바뀐 cat() 함수안에서부터 사용합니다.

```
 
52    func cat(r reader) {
53        const NBUF = 512
54        var buf [NBUF]byte

56        if *rot13Flag {
57            r = newRotate13(r)
58        }
59        for {
60            switch nr, er := r.Read(buf[:]); {
61            case nr < 0:
62                fmt.Fprintf(os.Stderr, "cat: error reading from %s: %s\n", r.String(), er.String())
63                os.Exit(1)
64            case nr == 0: // EOF
65                return
66            case nr > 0:
67                nw, ew := file.Stdout.Write(buf[0:nr])
68                if nw != nr {
69                    fmt.Fprintf(os.Stderr, "cat: error writing from %s: %s\n", r.String(), ew.String())
70                    os.Exit(1)
71                }
72            }
73        }
74    }
```

(main 안에서 wrapping을 하고 cat()은 매개변수의 타입을 변경 하는것 외엔 그냥 놔둘 수 있습니다만, 예제임을 감안하시기 바랍니다.) 56번째 줄에서 58번째 줄까지는 설정을합니다: 만일 rot13 flag가 true이면, reader를 rotate13으로 wrap하고 실행 합니다. 인터페이스 변수들은 포인터가 아니라 값이라는 것을 명심하시기 바랍니다: 매개변수의 형은 `*reader` 가 아니고 reader입니다. 심지어 struct를 가르키는 포인터를 가지고 있어도 마찬가지 입니다.

위 프로그램의 동작은 다음과 같습니다.

```
    $ echo abcdefghijklmnopqrstuvwxyz | ./cat
    abcdefghijklmnopqrstuvwxyz
    $ echo abcdefghijklmnopqrstuvwxyz | ./cat --rot13
    nopqrstuvwxyzabcdefghijklm
    $
```

의존성 주입(dependency injection)이 이루어 지는 것은 인터페이스가 파일 설명자 구현을 쉽게 교체하는 것을 허용하기 때문입니다.

인터페이스는 Go언어의 독특한 특징 입니다. 인터페이스는 만일 형이 인터페이스 안에 있는 모든 선언된 메소드들을 구현한다면 형에 의해 구현됩니다. 이것은 형이 서로다른 인터페이스들을 구현 할수 있다는 것을 의미 합니다. 형은 계층 구조가 없습니다; rot13에서 본것 처럼, 임시적으로 구현될 수 있습니다. file.File 형은 reader를 구현합니다; 이것은 writer를 구현할 수도 있고 혹은 현재 상황에 맞는 다른 메소드들로 부터 구성된 다른 인터페이스를 구현할 수도 있습니다.

```
    type Empty interface {}
```

모든 형은 빈 인터페이스를 구현하며, 이 인터페이스는 컨테이너 같은 것을 만들때 유용합니다.


## 정렬(Sorting) ##
인터페이스들은 간단한 형태의 다형성을 제공합니다. 인터페이스들은 오브젝트가 무엇을 하진지와 어떻게 하는것의 정의를 완벽하게 구분하기 때문에 같은 변수에 의해 각기 다르게 구현되는 것이 가능합니다.

예제로, progs/sort.go에서 가져온 간단한 정렬 알고리즘을 보도록 합시다.

```
13    func Sort(data Interface) {
14        for i := 1; i < data.Len(); i++ {
15            for j := i; j > 0 && data.Less(j, j-1); j-- {
16                data.Swap(j, j-1)
17            }
18        }
19    }
```

코드에는 sort 인터페이스로 wrap한 세개의 메소드만 필요합니다.

```
07    type Interface interface {
08        Len() int
09        Less(i, j int) bool
10        Swap(i, j int)
11    }
```

Len, Less, Swap 세개중 어느 형에도 Sort를 적용할 수 있습니다. sort 패키지는 정수, 문자열등의 배열을 정열하는데 필요한 메소드를 포함하고 있습니다; 다음은 int형의 배열을 위한 코드 입니다.

```
 
33    type IntArray []int

35    func (p IntArray) Len() int            { return len(p) }
36    func (p IntArray) Less(i, j int) bool  { return p[i] < p[j] }
37    func (p IntArray) Swap(i, j int)       { p[i], p[j] = p[j], p[i] }
```


non-struct 형을 위해 정의된 메소드를 볼수 있습니다. 여러분의 패키지에서 여러분이 정의하고 이름지은 어떤 형이던지 이를 위한 메소드를 정의 할 수 있습니다. 그리고, progs/sortmain.go에서 테스트 할 수 있습니다. 결과가 정확한지 테스트 하기 위해서, 이 프로그램은 sort 패키지에 있는 함수를 이용합니다만 여기선 sort 패키지에 대한 설명은 생략 하겠습니다.

```
 
12    func ints() {
13        data := []int{74, 59, 238, -784, 9845, 959, 905, 0, 0, 42, 7586, -5467984, 7586}
14        a := sort.IntArray(data)
15        sort.Sort(a)
16        if !sort.IsSorted(a) {
17            panic("fail")
18        }
19    }

```

만일 정렬 될 수 있는 새로운 형을 원한다면, 다음과 같이 그 형을 위한 세개의 메소드를 구현하기만 하면 됩니다.

```

30    type day struct {
31        num        int
32        shortName  string
33        longName   string
34    }

36    type dayArray struct {
37        data []*day
38    }


40    func (p *dayArray) Len() int            { return len(p.data) }
41    func (p *dayArray) Less(i, j int) bool  { return p.data[i].num < p.data[j].num }
42    func (p *dayArray) Swap(i, j int)       { p.data[i], p.data[j] = p.data[j], p.data[i] }

```

## 출력 (Printing) ##

지금깢지 서식있는 출력의 예제는 보통 이었습니다. 이 섹션에서는 Go 언어에서 서식있는 입출력이 어떻게 이루어지는지 다루어 보겠습니다.

앞에서 Printf, Fprintf등이 구현되어있는 fmt 패키지의 간단한 사용을 보았습니다. fmt 패키지 안에서 Printf는 다음과 같이 선언됩니다.

```
    Printf(format string, v ...interface{}) (n int, errno os.Error)
```

토큰 ... 은 가변 길이 매개변수 리스트를 나타내며, C에서는 stdarg.h 매크로를 이용해 다룹니다. Go에서는 가변길이 매개변수를 가지는 함수들은 특정한 형의 매개변수의 slice를 넘겨줍니다. Printf 의 경우, 선언은 ...interface{} 으로 되어 있고, 실제 형은 비어있는 인터페이스 값인 [.md](.md)interface{}의 slice 입니다. Printf는 매개변수 slice의 각 요소를 반복적으로 확인해서 타입에 맞게 스위칭 하거나 값에 맞도록 라이브러리를 이용해 변경 합니다. 이 문서의 주제에서 벗어나지만, 실시간 형분석은 Go의 Printf의 좋은 특성인 역동적인 매개변수의 형을 분석하는 능력을 잘 설명합니다.

예를 들어, C에서 각 포멧은 각 매개변수의 형에 맞아야 합니다. Go에서는 많은 경우 좀 더 쉽습니다. %llud대신에 %d만 쓰면 됩니다; Printf는 크기와 정수의 부호를 알고 적절하게 동작 합니다. 다음 코드는

```
 
10        var u64 uint64 = 1<<64-1
11        fmt.Printf("%d %d\n", u64, int64(u64))
```

다음과 같이 출력 됩니다.

```
    18446744073709551615 -1
```

사실, 만일 포멧을 쓰는게 귀찮다면, %v 를 사용하면 단순히 적절한 형식, 어떤 값, 심지어 배열이나 구조체에도 사용 가능합니다. 다음 코드

```
 
14        type T struct {
15            a int
16            b string
17        }
18        t := T{77, "Sunset Strip"}
19        a := []int{1, 2, 3, 4}
20        fmt.Printf("%v %v %v\n", u64, t, a)
```

의 출력은 다음과 같습니다.

```
    18446744073709551615 {77 Sunset Strip} [1 2 3 4]
```
Printf 대신 Print나 Println을 사용하면 출력형식 지정을 하지 않아도 됩니다. 이런 루틴들은 출력 형식지정이 완전 자동으로 이루어 집니다. Print함수는 %v와 동일하게 단지 자신의 요소들을 출력합니다. 반면, Println은 매개변수 사이에 빈칸을 삽입하고 새로운 줄을 더합니다. 다음 두 줄의 출력은 위에서 호출한 Printf의 출력과 완전히 동일 합니다.

```
 
21        fmt.Print(u64, " ", t, " ", a, "\n")
22        fmt.Println(u64, t, a)
```

만일, Printf나 Print에 출력형식을 지정하고 싶으면, 문자열을 반환하는 String() 메소드를 전달 하면 됩니다. print루틴들은 메소드의 구현이 필요한지 값을 확인해서, 만일 필요하다면, 다릍 포멧 대신 사용할 것 입니다. 다음은 간단한 예제 입니다.

```
 
09    type testType struct {
10        a int
11        b string
12    }

14    func (t *testType) String() string {
15        return fmt.Sprint(t.a) + " " + t.b
16    }


18    func main() {
19        t := &testType{77, "Sunset Strip"}
20        fmt.Println(t)
21    }
```


`*testType`이 String()메소드를 가지고 있기 때문에, 그 형을 위한 기본 출력형식 지정자가 사용되어 출력을 생성할 것입니다.

```
    77 Sunset Strip
```

String() 메소드가 출력형식 지정을 위해 Sprint(문자열을 반환하는 Go의 함수)를 호출하는 것을 보시기 바랍니다; 특별한 출력지정자는 재귀적으로 fmt 라이브러리를 이용할 수 있습니다.

Printf의 또다른 특징은 값의 형을 문자열로 표시해주는 %T 포멧 이며, 다형성 코드를 디버깅 하는데 유용합니다.

flag와 정확도를 가진 완전한 자신만의 고유 형식을 출력하는 것도 가능합니다만, 주된 내용에서 벗어나기 때문에 다음 예제로 남겨 놓겠습니다.

Printf가 어떻게 String() 메소드의 구현여부를 아는지 궁금해 하실지도 모르겠습니다. 실제로, 만일 값이 구현된 인터페이스로 변형될 수 있는지를 물어보는 것으로 판단 합니다. 값 v가 주어지면 다음과 같습니다:

```
    type Stringer interface {
        String() string
    }
```

```
    s, ok := v.(Stringer)  // Test whether v implements "String()"
    if ok {
        result = s.String()
    } else {
        result = defaultOutput(v)
    }
```

코드는 "type assertion" (v.(Stringer))를 사용하여 v에 저장된 값이 Stringer 인터페이스를 만족하는지 테스트 합니다; 만일 만족한다면, s는 메소드를 구현하는 인터페이스 값이 되고, ok는 true가 될 것 입니다. 그리고 나서, 메소드를 호출하기 위해 인터페이스 값을 사용합니다. ("쉼표, ok" 패턴은 Go언어에서 형 변환, 맵 업데이트, 커뮤니케이션 등에서 성공여부를 테스트 하는데 사용되는 형식 이지만, 이 자습서에는 한번 나옵니다.) 만일 값이 인터페이스를 만족시키지 못하면 ok는 false값을 가지게 됩니다.

코드의 일부분인 Stringer라는 이름은 "[e](e.md)r"을 인터페이스에 추가하여 단순한 메소드셋을 나타내는 관습을 따릅니다.

마지막으로, Printf 등 과 Sprintf 외에 Fprintf등도 있습니다. C와 달리, Fprintf의 첫 매개변수는 파일이 아닙니다. 대신, io.Writer 형을 가지는 변수입니다. 이 변수는 io 라이브러리에 정의되어 있는 인터페이스 형 입니다:

```
    type Writer interface {
        Write(p []byte) (n int, err os.Error)
    }
```

(이 인터페이스는 또다른 관습적인 이름인 Writer입니다; ioReader, io.ReadWriter 등도 있습니다) 그래서, 파일 뿐만 아니라 네트워크 채널, 버퍼등 원하는 어떤 것에 대해서 표준 Write()메소드를 구현하기만 하면 어떤 형에서도 Fprintf를 부를 수 있습니다.

## 소수 (Prime numbers) ##

이제 프로세스와 동시 통신 프로그램에 대해 알아보겠습니다. 이 주제는 너무 커서 익숙한 이 토픽에 익숙하다고 가정하고 시작하겠습니다.

소수의 체라는 전통적인 프로그램이 있습니다. (에라토테네스(Eratosthenes)의 체는 여기서 소개하는 알고리즘보다 좀 더 효율적 입니다. 하지만 지금은 알고리즘 보다는 동시성에 좀 더 중점을 두겠습니다.) 알고리즘은 각각의 소수로 구성된 필터를 만든 다음 자연수의 시퀀스를 집어넣어서 각 소수의 배수가 되는 숫자를 제외 하는 방식으로 동작 합니다. 각 단계에서 소수들의 필터의 열을 가지고 있고 튀어 나오는 다음 숫자는 다음 소수가 다 되고, 체인 안에서 다음 필터를 구성합니다.

다음은 흐름도 입니다; 각 상자는 이전의 필터로 부터 나오는 첫번째 숫자로 구성된 필터를 나타 냅니다.

![http://golang.org/doc/sieve.gif](http://golang.org/doc/sieve.gif)



정수 스트림을 생성하기 위해, Go channel을 사용합니다. Go channel은 CSP에서 파생된 것으로서, 두개의 동시 계산간에 연결을 할 수 있는 통신 채널을 나타냅니다. Go에서는 채널 변수들은 통신을 조절하는 실시간 객체를 참조합니다; map과 slice처럼 새로운 채널을 생성하기 위해 make를 사용합니다.

다음은 progs/sieve.go 에있는 첫번째 함수 입니다:

```
 
09    // Send the sequence 2, 3, 4, ... to channel 'ch'.
10    func generate(ch chan int) {
11        for i := 2; ; i++ {
12            ch <- i  // Send 'i' to channel 'ch'.
13        }
14    }
```

generate 함수는 2, 3, 4, 5, ... 숫자열을 바이너리 통신 연산자 <- 를 사용하여 매개변수 채널 ch에 보냅니다. 채널 동작은 만일 ch에 수신된 값이 없다면, 전송 동작을 막아서 수신이 이루어질 때 까지 기다리도록 합니다.


filter 함수는 세개의 매개변수를 가지고 있습니다: 입력 채널, 출력 채널, 소수. 이 함수는 소수에 의해 나누어 지는 값을 제외하고 입력 값을 출력으로 복사 합니다. 단항 통신 연산자 <- (수신) 는 채널에 있는 다음 값을 가져옵니다.

```
 
16    // Copy the values from channel 'in' to channel 'out',
17    // removing those divisible by 'prime'.
18    func filter(in, out chan int, prime int) {
19        for {
20            i := <-in  // Receive value of new variable 'i' from 'in'.
21            if i % prime != 0 {
22                out <- i  // Send 'i' to channel 'out'.
23            }
24        }
25    }
```

generator와 필터들은 동시에 실행 됩니다. Go는 고유한 process/threads/light-weight processes/coroutines 모델을 가지고 있지만, 표기 혼란을 피하기 위해 동시에 계산을 수행 하는 것을 Go 에서는 goroutines라고 부르도록 하겠습니다. goroutine을 시작하기 위해, 키워드 go 로 함수를 호출합니다; 이렇게 시작하면 함수가 같은 주소 공간에서 현재 수행되는 계산과 동시에 병행으로 수행됩니다.

```
    go sum(hugeArray) // calculate sum in the background
```

만일 언제 계산이 끝나는지 알고 싶다면 채널을 통해 결과를 넘겨 받을 수 있습니다.

```
    ch := make(chan int)
    go sum(hugeArray, ch)
    // ... do something else for a while
    result := <-ch  // wait for, and retrieve, result
```

다시 소수의 체로 돌아가보겠습니다. 다음은 체의 파이프라인이 어떻게 같이 붙어 있는지 보여줍니다.

```
 
28    func main() {
29        ch := make(chan int)  // Create a new channel.
30        go generate(ch)  // Start generate() as a goroutine.
31        for i := 0; i < 100; i++ { // Print the first hundred primes.
32            prime := <-ch
33            fmt.Println(prime)
34            ch1 := make(chan int)
35            go filter(ch, ch1, prime)
36            ch = ch1
37        }
38    }
```

29번째 줄에서 채널을 초기화 해서 생성하여 generate에 전달하고 나면 채널이 시작 되는 것을 볼 수 있습니다. 각 소수가 채널에서 나오면 새로운 filter가 파이프라인에 추가가 되고 그 filter의 출력은 ch의 새로운 값이 됩니다.

체 프로그램은 이런 스타일 프로그래밍에서 일반적인 패턴을 사용하여 변경할 수 있습니다. 다음은 generate의 변경된 버젼이며, progs/sieve1.go에 있습니다:

```
 
10    func generate() chan int {
11        ch := make(chan int)
12        go func(){
13            for i := 2; ; i++ {
14                ch <- i
15            }
16        }()
17        return ch
18    }

```

이 버젼은 모든 설정을 내부적으로 수행 합니다. 출력 채널을 생성하고, goroutine실행을 개시하여 함수 literal을 실행하고, 호출자에게 채널을 반환 합니다. 이것은 goroutine을 시작하고 그 연결을 반환하는 동시 실행을 위한 공장 입니다.

함수 literal (12~16줄)은 익명 함수를 수성하도록 해주고 특정 시점에 수행 됩니다. 지역 변수 ch는 함수 literal에 사용가능 하고, 심지어 generate가 반환된 후(끝난 후)에도 살아 남습니다.

filter에도 같은 변경을 할 수 있습니다:

```
 
21    func filter(in chan int, prime int) chan int {
22        out := make(chan int)
23        go func() {
24            for {
25                if i := <-in; i % prime != 0 {
26                    out <- i
27                }
28            }
29        }()
30        return out
31    }
```

결과적으로 sieve 함수의 주 루프는 더 단순하고 명확해 졌습니다. 그리고 이것도 동시 실행 공장으로 변환 합니다.

```

33    func sieve() chan int {
34        out := make(chan int)
35        go func() {
36            ch := generate()
37            for {
38                prime := <-ch
39                out <- prime
40                ch = filter(ch, prime)
41            }
42        }()
43        return out
44    }
```


이제, 소수의 체에 대한 main의 인터페이스는 소스의 채널 입니다:

```

46    func main() {
47        primes := sieve()
48        for i := 0; i < 100; i++ { // Print the first hundred primes.
49            fmt.Println(<-primes)
50        }
51    }
```



## 멀티플렉싱 (Multiplexing) ##

채널을 이용하면, 멀티플렉서를 명시적으로 기술하지 않고 여러개의 독립적인 goroutine 클라이언트들을 사용하는 것이 가능합니다. 원래 송신자에게 회신하도록 사용될 체널이 있는 메세지를 서버에 보내는 방식 입니다. 실제 클라이언트-서버 프로그램은 코드의 양이 많습니다. 그래서 여기서는 아이디어를 간단하게 보여줄 수 있도록 매우 단순한 예를 보여 드리겠습니다. 회신을 위해 사용되는 채널이 포함된 request 형을 정의 하는 것으로 시작 합니다.

```
 
09    type request struct {
10        a, b    int
11        replyc  chan int
12    }
```

서버는 단순합니다: 정수들의 단순한 바이너리 연산을 수행합니다. 코드들은 연산을 수행 하고 요청에 응답을 합니다.

```
 
14    type binOp func(a, b int) int

16    func run(op binOp, req *request) {
17        reply := op(req.a, req.b)
18        req.replyc <- reply
19    }

```

14번째 줄에서 binOp를 정의 하고 이 binOp는 두개의 정수를 매개변수로 하고 세번째 정수를 반환하는 함수가 됩니다.

server 루틴은 무한 루푸 입니다. 요청을 받고, 긴 실행 시간동안 멈추는 것을 막기 위해 실제 일을 하기 위한 goroutine을 시작 합니다.

```
 
21    func server(op binOp, service chan *request) {
22        for {
23            req := <-service
24            go run(op, req)  // don't wait for it
25        }
26    }

```

익숙한 방식으로 서버를 구축 합니다. 서버를 시작 한 후 연결될 채널을 반환 합니다.

```
 
28    func startServer(op binOp) chan *request {
29        req := make(chan *request)
30        go server(op, req)
31        return req
32    }

```

다음은 간단한 테스트 입니다. 추가적인 연산자와 응답에 대한 기다림 없이 N 요청을 보내는 것으로 서버를 시작합니다. 모든 요청들이 보내진 다음에야 결과를 체크 합니다.

```

34    func main() {
35        adder := startServer(func(a, b int) int { return a + b })
36        const N = 100
37        var reqs [N]request
38        for i := 0; i < N; i++ {
39            req := &reqs[i]
40            req.a = i
41            req.b = i + N
42            req.replyc = make(chan int)
43            adder <- req
44        }
45        for i := N-1; i >= 0; i-- {   // doesn't matter what order
46            if <-reqs[i].replyc != N + 2*i {
47                fmt.Println("fail at", i)
48            }
49        }
50        fmt.Println("done")
51    }

```

곤란하게도 이 프로그램은 서버를 깔끔하게 멈출 수 없습니다; main 이 통신중에 몇개의 수행시간이 긴 goroutin들이 막고 있을때 그렇습니다. 이런 문제를 해결하기 위해, 서버에 quit 채널을 제공합니다.

```

32    func startServer(op binOp) (service chan *request, quit chan bool) {
33        service = make(chan *request)
34        quit = make(chan bool)
35        go server(op, service, quit)
36        return service, quit
37    }

```

다음과 같이 server함수에 quit 채널을 전달 합니다.

```

21    func server(op binOp, service chan *request, quit chan bool) {
22        for {
23            select {
24            case req := <-service:
25                go run(op, req)  // don't wait for it
26            case <-quit:
27                return
28            }
29        }
30    }

```

server 안에 select 문이 상황에 맞게 리스트된 다중 통신들중 어떤 것이 실행될지 선택 합니다. 만일 모든 것이 막혀 있다면, 한개가 실행 될 때까지 기다립니다; 만일 복수개가 실행 될 수 있다면, 무작위로 한개를 선택 합니다. 이런 식으로 select는 실행을 끝내는 quit 메세지를 받을 때까지 요청을 처리 하게 됩니다.

남은 것은 main의 끝에 quit 채널을 보내는 것 입니다.

```
40        adder, quit := startServer(func(a, b int) int { return a + b })
```

...

```
 
55        quit <- true
```


일반적으로 더 많은 Go 프로그램과 동시진행 프로그램이 있지만, 이 짧은 문서는 기본적인 몇가지만 제공 합니다.