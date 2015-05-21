# 요약 #
  * 내용 : C++개발자를 위한 Go언어 소개
  * 원문 : [Go For C++ Programmers](http://golang.org/doc/go_for_cpp_programmers.html)
  * 번역자 : jeyong@gmail.com




# C++개발자를 위한 Go언어 소개 #
Go는 C++처럼 다목적 시스템 언어의 의도를 가지고 시작한 시스템 프로그래밍 언어이다. C++ 경험이 있는 개발자가 Go를 익히기 위해 유념해야 할 것들이 있다. 이 문서는 Go와 C++ 간의 차이점을 논하며 유사한 점에 대해서는 거의 다루지 않는다.

Go에 대한 일반적인 소개는 Go tutorial과 Effective Go를 참고하자.
Go 언어의 상세한 정보는 Go spec을 참고하라.

## 차이점 ##
  * Go는 생성자와 소멸자는 가지는 class를 가지지 않는다. class의 method 대신에 class는 hierarchy, 가상 함수를 상속하며 interface를 제공한다. interface는 C++이 template를 사용하는 것처럼 사용된다.
  * Go는 가비지 콜렉션(garbage collection)을 이용한다. 메모리를 명시적으로 해제하기 위해 필요한 것은 아니다. 가비지 콜렉션은 현대 프로세서로 오면서 요구가 늘어났고 또한 아주 효과적이다.
  * Go는 포인터는 있지만 포인터 연산은 제공하지 않는다. string의 byte를 검사하는데 포인터 변수를 사용할 수 없다.
  * Go에서 array는 매우 중요하다. array가 함수의 인자로 사용될 때, 함수는 포인터가 아니라 array의 복사본을 받는다. 그러나 실제 함수에서는 인자로 slice를 자주 사용한다. slice는 array에 대한 포인터를 가진다. slice는 아래에서 더 다루기로 한다.
  * string은 Go에서 제공한다. 일단 생성되면 변경되지 않는다.
  * 해쉬 테이블은 Go에서 제공한다. map이라 부른다.
  * 개별 실행 thread와 이들간의 통신 채널(communication channels)은 Go에서 제공한다. 아래에서 더 다루기로 하자.
  * 특정 type(map, channel 등)들은 값에 의한 전달이 아니라 참조에 의한 전달(passed by reference)을 사용한다. 즉, map을 함수에 전달하는 경우 map을 복사하는 것이 아니며 만약 함수에서 map을 변경하면 호출자(caller)도 해당 변경에 영향을 받는다. C++로 말하자면 이런 것들을 참조 타입(reference types)라고 볼 수 있다.
  * Go는 헤더 파일을 사용하지 않는다. 대신 각 소스 파일은 정의한 package의 일부가 된다. package가 대문자로 시작하는 객체(type, 상수, 변수, 함수)을 정의하면 해당 객체는 이 package를 import시키는 파일에서 접근 가능하다.
  * Go는 암시적 타입 변환(implicit type conversion)을 지원하지 않는다. 다른 type들을 혼합하여 사용하는 연산은 캐시팅(cast)이 필요하다.(Go에서는 변환(conversion)이라 부른다.)
  * Go는 함수 오버로딩(overloading)을 지원하지 않으며 사용자 정의 연산도 지원하지 않는다.
  * Go는 const나 volatile와 같은 지정자를 지원하지 않는다.
  * Go는 유효하지 않은 포인터에 대해서 nil을 사용하지만 C++은 NULL이나 0을 사용한다.

## 문법 ##
선언문법은 C++과 비교하면 반대가 된다. 이름을 쓰고 타입을 적는다. C++과 달리 타입에 대한 문법은 변수가 사용하는 방식과 일치하지 않는다. 타입선언은 왼쪽에서 오른쪽으로 쉽게 읽을 수 있다.
```
Go                           C++
var v1 int                // int v1;
var v2 string             // const std::string v2;  (approximately)
var v3 [10]int            // int v3[10];
var v4 []int              // int* v4;  (approximately)
var v5 struct { f int }   // struct { int f; } v5;
var v6 *int               // int* v6;  (but no pointer arithmetic)
var v7 map[string]int     // unordered_map<string, int>* v7;  (approximately)
var v8 func(a int) int    // int (*v8)(int a);
```

선언은 일반적으로 선언 대상의 이름 앞에 키워드(keyword)를 갖는다. 키워드는 var, func, const나 type 중에 하나다. 메소드 선언은 선언 대상의 이름 앞에 수신자(receiver)가 있다는 약간의 예외가 있다. 인터페이스 부분 참조

아래와 같이 키워드 뒤에 괄호가 있고 괄호 내부에 선언을 사용할 수도 있다.
```
var (
    i int
    m float64
)
```

함수를 선언하는 경우 각 인자에 대한 이름을 제공해야만 하거나 특정 인자에 대해서는 이름을 제공하지 않는다. 일부 이름들은 생략하지 못한다. 동일한 타입을 가지는 다양한 이름을 그룹으로 묶을 수 있다.
```
func f(i, j, k int, s, t string)
```

변수는 선언할 때 초기화 할 수 있다. 선언 후에 타입을 지정할 수 있는데 반드시 해야하는 것은 아니다. 타입을 지정하지 않으면 변수의 타입은 초기화에 사용한 타입으로 결정된다.
```
var v = *p
```

아래 상수와 관련된 부분을 보자. 변수가 명시적으로 초기화 되지 않았다면, 타입을 반드시 지정해야만 한다. 이런 경우 해당 타입의 0 값으로 암시적으로 초기화 된다.

함수 내부에서 짧게 선언하는 문법으로 ':='이 가능하다.
```
v1 := v2
```

이는 아래와 동일하다.
```
var v1 = v2
```

Go는 다중 할당을 허용하는데 병렬로 처리 된다.
```
i, j = j, i    // Swap i and j.
```

함수는 여러개의  리턴 값을 가질 수도 있다. 이럴 경우 괄호내의 리스트로 표시한다. 리턴한 값은 변수의 리스트에 할당함으로써 저장할 수 있다.
```
func f() (i int, j int) { ... }
v1, v2 = f()
```

Go 코드는 거의 세미콜론(;)을 사용하지 않는다. 기술적으로 볼 때, 모든 Go 구문은 세미콜론으로 끝난다. 그러나 Go는 해당 행이 명확하게 완성된 경우라면 blank없는 행의 끝을 세미콜론으로 처리한다.(Go언어 스펙 참조) 이런 결과로 line break를 사용하지 못하는 경우가 발생한다. 예제는 아래와 같다. 이렇게 사용할 수 없다.
```
func g()
{                  // INVALID
}
```

세미콜론은 g() 후에 추가되어 함수 정의가 아니라 함수 선언이 된다. 비슷한 경우로 아래와 같이 사용할 수 없다.
```
if x {
}
else {             // INVALID
}
```

세미콜론은 }와 else 사이에 들어가서 문법 오류가 발생한다.
세미콜론은 구문의 끝을 처리하므로 C++처럼 계속 사용할 수 있다. 그러나 추천하는 스타일은 아니다. 이상적인 Go 코드는 불필요한 세미콜론을 생략한다. 실제적으로 for루프와 한 행내에 여러 짧은 구문들을 작성할 때를 제외하고는 생략한다.

세미콜론이나 괄호에 신경쓰는 것보다는 gofmt를 이용해서 여러분의 코드를 포맷에 맞추는 작업이 가능하다. 한가지 표준의 Go 스타일로 코드를 생성해주어 포맷에 맞추는 것보다는 코드에 더 신경쓸 수 있게 한다. 스타일은 초기에 이상할 수 있지만 다른 스타일만큼 훌륭하고 친근해 지면 편안해 질 것이다.

struct에 대한 포인터를 사용할 때 '->'대신 '.'을 사용한다. 결국 문법적으로 struct에 대한 struct건 포인터건 동일한 방식으로 사용된다.
```
type myStruct struct { i int }
var v9 myStruct              // v9 has structure type
var p9 *myStruct             // p9 is a pointer to a structure
f(v9.i, p9.i)
```

Go는 if, for, switch구문 주위에 괄호가 필요없다. 반면에 중괄호는 if와 for구문의 body에는 필요하다.
```
if a < b { f() }             // Valid
if (a < b) { f() }           // Valid (condition is a parenthesized expression)
if (a < b) f()               // INVALID
for i = 0; i < 10; i++ {}    // Valid
for (i = 0; i < 10; i++) {}  // INVALID
```

Go는 while구문 혹은 do/while구문이 없다. for구문에 단일 조건을 사용해서 while구문을 대체할 수 있다. 조건부분을 생략하면 무한 루프가 된다.

Go는 레이블(label)을 지정하기 위해 break와 continue을 사용할 수 있다. 레이블은 반드시 for, switch, select구문에 속해 있어야 한다.

switch구문에서 case 레이블은 그냥 통과(fall through)하지 않는다. fallthrough 키워드를 이용해서 통과하도록 만들 수 있다. 바로 다음 case에도 동일하게 적용된다.
```
switch i {
case 0:  // empty case body
case 1:
    f()  // f is not called when i == 0!
}
```
하지만 case는 다양한 값을 가질 수 있다.
```
switch i {
case 0, 1:
    f()  // f is called if i == 0 || i == 1.
}
```

case에 사용하는 값은 상수(constant)나 정수(integers)일 필요는 없다. 동일값 비교 연산자(equality comparison operator)를 지원하는 어떤 타입이라도 가능하다. 예를 들면, string, porint도 가능하다. switch 값이 생략된 경우에는 기본값은 true가 된다.
```
switch {
case i < 0:
    f1()
case i == 0:
    f2()
case i > 0:
    f3()
}
```

++과  -- 연산자는 표현식(expression)이 아니라 구문에서만 사용된다. c = `*p++`를 사용할 수 없다. `*p++`은 `(*p)++`로 파싱된다.

defer구문은 defer구문을 포함하는 함수가 리턴한 후에 특정 함수를 호출할 때 사용된다.
```
fd := open("filename")
defer close(fd)         // fd will be closed when this function returns.
```

## 상수(Constants) ##
Go에서 상수는 타입을 갖지 않을(untyped) 수 있다. 만약 선언에서 타입이 없고 초기화 할때 untyped 상수만 사용하게 되면 const로 선언된 이름을 가지는 상수에도 적용된다. untyped 상수에서 유도된 값은 타입을 가지는 값을 필요로 하는 컨텍스트내에서 사용되면 typed 상수가 된다. 이렇게 되면 상수들을 일반적인 암시적 타입 변환(implicit type conversion) 없이도 비교적 자유롭게 사용할 수 있다.
```
var a uint
f(a + 1)  // untyped numeric constant "1" becomes typed as uint
```

untyped 상수의 크기에 대해서 어떠한 제한도 두지 않았다. 상수가 타입이 필요한 곳에 사용될 때만 제한을 두었다.

```
const huge = 1 << 100
f(huge >> 98)
```

Go는 enum을 지원하지 않는다. 대신에 iota라는 것을 사용하여 연속적으로 증가하는 값을 얻는데  한번의 const 선언으로 대신할 수 있다. const를 밖으로 빼서 한 번 만 const로 사용했다.
```
const (
    red = iota   // red == 0
    blue         // blue == 1
    green        // green == 2
)
```

## 슬라이스(Slices) ##
개념적으로 slice는 3개의 필드를 가지는 struct이다. 3개의 필드는 array, length, capacity다. slice는 array의 요소들에 접근하기 위해 `[]`연산자를 제공한다. len 함수는 slice의 길이를 리턴하는 내장 함수이다. cap은 capacity를 리턴하는 내장함수다.

주어진 array 혹은 slice에서 a`[I:j]`를 이용해서 새로운 slice를 생성한다. a를 참조하며 I부터 시작해서 J 직전까지의 인덱스를 가지는 새로운 slice를 생성한다. 길이는 J - I가 된다. a가 참조하는 동일한 array를 참조하는 새로운 slice가 된다. 즉, 새로운 slice를 이용해서 생기는 변경은 a를 이용해서 볼 수도 있다. 새로운 slice의 capacity는 단순히 -I이다. array의 capacity는 array의 길이다. slice 타입의 변수에 array포인터를 할당할 수 있다. var s `[]`int; var a`[10]` int가 있을 때 s = &a와 같은 할당은 s = a`[0:len(a)]`와 동일하다.

slice 문법은 string과 사용할 수도 있다. 이때는 원래 문자열의 부분문자열을 값으로 가지는 새로운 string을 리턴한다. string은 변경이 불가능(immutable)하므로 string slice는 slice의 내용을 새로 저장할 저장소를 할당받지 않고 구현할 수 있다.
## 값 할당(Making values) ##
Go는 new라는 내장 함수는 갖는데, 타입을 가지고 힙(heap)에 공간을 할당한다. 할당받은 공간은 해당 타입을 0값으로 초기화한다. 예를 들어, new(int)는 힙에 새로운 Int를 할당하고 그 값을 0으로 초기화하여 해당 주소를 리턴한다. `*int` 타입을 갖게 된다. C++과 달리 new는 연산자가 아니라 함수이다. new int로 사용하면 문법 오류가 발생한다.

map과 channel 값은 내장함수인 make를 사용해서 할당받아야 한다. 초기화 없이 map이나 channel타입으로 선언된 변수는 자동으로 nil로 초기화 된다. `make(map[int][int])`를 호출하면 `map[int]int` 타입의 값을 새로 할당받아 리턴한다. make는 값을 리턴하지 포인터를 리턴하지 않는다. map과 channel 값이 참조로 전달(passed by reference)라는 사실과 일치한다. map타입으로 make를 호출하면 map의 예상 capacity 값을 인자로 넣을 수도 있다. channel타입으로 make를 호출하면 channel의 버퍼링 capacity 설정값을 인자로 넣을 수도 있다. 인자를 사용하지 않는 경우 0이 되며 unbuffered로 설정된다.

make함수는 slice를 할당하는데 사용할 수 있다. 이 경우 원 array를 위해 메모리를 할당하고 array를 참조하는 slice를 리턴한다. 첫번째 인자로 slice에 있는 요소들의 개수이다.  두번째 인자는 slice의 capacity인데 이는 옵션이다. 예로 `make([]int, 10, 20)`를 보자. `new([20]int)[0:10]`과 동일하다. Go가 가비지 콜렉션(garbage collection)을 사용하므로 새로 할당받은 array는 리턴받은 slice에 대한 참조가 더이상 없으면 제거될 수도 있다.

## 인터페이스(Interface) ##
C++은 class, subclass, template를 제공하지만 Go는 interface를 제공한다. Go에서 interface는 C++에서 순수 추상 클래스(pure abstract class)와 유사하다. 이 클래스는 멤버 데이터가 없고 완전히 순수 가상 method들을 가진다. 그러나 Go에서 interface내에 이름을 가진 method들을 제공하는 type은 interface의 구현으로 여길 수 있다. 어떠한 명시적으로 선언된 상속도 필요없다. interface의 구현은 완전히 interface 자체와 분리되어 있다.

method는 수신자(receiver)를 가진다는 점을 제외하고는 일반 함수 정의와 비슷하다. 수신자는 C++ 클래스 method에서 this 포인터와 유사하다.
```
type myType struct { i int }
func (p *myType) get() int { return p.i }
```

위 소스는 myType과 관련된 get method를 선언한다. 수신자는 함수에서 p라고 이름 붙였다.

method들은 이름을 가지는 type으로 정의한다. 만약 값을 다른 type으로 변환한다면 새로운 값은 이전 type이 아니라 새로운 type의 method를 가지게 된다.

여러분은 내장 type에서 method를 새로운 이름을 가지는 타입을 선언함으로써 정의할 수도 있다. 새로운 type은 내장 type과는 구별된다.

```
type myInteger int
func (p myInteger) get() int { return int(p) } // 변환이 필요하다.(Conversion required.)
func f(i int) { }
var v myInteger
// f(v) 유효하지 않다.
// f(int(v))는 유효하다; int(v)는 정의한 method를 가지고 있지 않다.
```

아래 interface가 주어지면
```
type myInterface interface {
	get() int
	set(i int)
}
```

아래를 추가함으로써 interface를 만족시키는 myType을 만들 수 있다.
```
func (p *myType) set(i int) { p.i = i }
```

자 이제 myInterface를 인자로 가지는 어떠한 함수도 `*myType` 타입의 변수를 수용할 수 있게 되었다.

```
func getAndSet(x myInterface) {}
func f1() {
	var p myType
	getAndSet(&p)
}
```

다르게 말하면 만약 C++ 순수 추상 기본 클래스로서 myInterface를 본다면, `*myType`을 위한 set과 get을 정의하는 것은 `*myType`이 자동으로 myInterface으로부터 자동으로 상속받게 만든 것이다. type은 다중 interface를 만족시킨다.

익명의 필드(anonymous field)는 C++의 자식 클래스와 같이 어떤 것을 구현하는데 사용할 수 있다.
```
type myChildType struct { myType; j int }
func (p *myChildType) get() int { p.j++; return p.myType.get() }
```

myType의 자식으로서 효과적으로 myChildType을 구현한다.
```
func f2() {
	var p myChildType
	getAndSet(&p)
}
```

set 메소드는 myChildType에서 상속는다. 왜냐하면 익명 필드와 관련된 메소드들은 클리징 타입(closing type)의 메소드가 되기 때문이다. 이런 경우 myChildType은 myType의 익명 필드를 가지기 때문에 myType의 메소드들은 또한 myChildType의 메소드가 된다. 이 예제에서 get 메소드는 오버라이드(override)되고 set 메소드는 상속받는다.

이는 C++의 자식 클래스와 정확히 일치하지는 않는다. 익명 필드의 메소드가 호출될 때, 수신자는 surrounding struct가 아닌 필드이기 때문이다. 다른 말로 하자면, 익명 필드에서 메소드는 가상 함수(virtual function)가 아닌 것이다. 가상 함수와 동일한 것을 원한다면 interface를 사용하라.

interface 타입을 가지는 변수는 type assertion이라 불리는 특별한 생성자를 이용해서 다른 인터페이스 타입을 가지도록 변환할 수도 있다. C++의 dynamic\_cast와 같이 런타임 시에 동적으로 구현된다. dynamic\_cast와는 다르게 2개 interface들 사이에는 관계선언도 필요없다.

```
type myPrintInterface interface {
  print()
}
func f3(x myInterface) {
	x.(myPrintInterface).print()  // type assertion to myPrintInterface
}
```

myPrintInterface로 변환은 완전히 동적이다. x의 기본이 되는 타입이 print 메소드를 정의하는한 동작할 것이다.

변환이 동적으로 이루어지므로 generic 프로그래밍을 구현하는 것은 C++에서 템플릿(template)과 유사하게 사용할 수 있다. 최소한의 인터페이스의 값들을 조작함으로 할 수 있다.
```
type Any interface { }
```

컨테이너(container)는 Any라는 용어를 사용한다. 하지만 호출자는 반드시 포함된 타입의 값을 복구하기 위해서 type assertion을 사용해서 꺼내야 한다. 티입을 결정하는 것은 정적(static)이라기 보다는 동적(dynamic)이므로 C++ 템플릿이 관련 연산을 인라인으로 나타내는 방식과 동일하지는 않다. 연산은 런타임에 완전히 타입을 검사하겠지만 모든 연산들이 함수 호출과 관련을 가지게 될 것이다.
```
type iterator interface {
	get() Any
	set(v Any)
	increment()
	equal(arg *iterator) bool
}
```

## Goroutines ##
Go에서 go구문을 이용해서 새로운 쓰레드(goroutine)를 실행시킬 수 있다. go 구분은 새로 생성한 goroutine으로 약간 다른 방식으로 함수를 실행시킨다. 동일 프로그램내에서 모든 goroutine은 동일한 주소 공간을 공유한다.

내부적으로 goroutine은 다중 운영시스템 쓰레드(multiple operating system thread)들 사이에서 멀티플렉스된 coroutine처럼 동작한다. 여러분은 상세내용에 대해서 걱정할 필요가 없다.
```
func server(i int) {
    for {
        print(i)
        sys.sleep(10)
    }
}
go server(1)
go server(2)
```

(server함수에서 for 구문은 C++에서 while(true)루프와 동일하다.)
goroutine은 비용이 적게 든다.

Go에서 클로져(closures)로 구현하는 함수 리터럴은 go 구문과 함께 유용하다.

```
var g int
go func(i int) {
	s := 0
	for j := 0; j < i; j++ { s += j }
	g = s
}(1000)  // Passes argument 1000 to the function literal.
```

## 채널(Channels) ##
channel은 goroutine들 사이의 통신에 사용된다. channel을 통해서 모든 값을 보낼 수 있다. channel은 효율적이며 비용이 적게 든다. 값을 channel로 보내기 위해서 연산자로 <-를 사용한다. channel에서 값을 받기 위해서 <-를 사용한다. 함수를 호출할 때, channel은 참조로 전달(passed by reference)된다.

Go 라이브러리는 뮤텍스(mutexes)를 제공하지만 공유 channel을 가지고 단일 goroutine을 사용할 수도 있다. 유일한 값에 접근을 제어하는 manager 함수를 사용하는 예제를 보자.
```
type cmd struct { get bool; val int }
func manager(ch chan cmd) {
	var val int = 0
	for {
		c := <- ch
		if c.get { c.val = val; ch <- c }
		else { val = c.val }
	}
}
```

이 예제에서 동일 channel을 입력과 출력에 사용한다. 동시에 해당 manager로 여러 goroutine이 통신하는 경우에는 문제가 있다.  manager로부터 응답을 기다리는 goroutine은 다른 goroutine으로부터 요청을 받을 수도 있다. 해결책은 channel로 전달하는 것이다.

```
type cmd2 struct { get bool; val int; ch <- chan int }
func manager2(ch chan cmd2) {
	var val int = 0
	for {
		c := <- ch
		if c.get { c.ch <- val }
		else { val = c.val }
	}
}
```

manager2를 사용하기 여기서 채널을 주자.
```
func f4(ch <- chan cmd2) int {
	myCh := make(chan int)
	c := cmd2{ true, 0, myCh }   // Composite literal syntax.
	ch <- c
	return <-myCh
}
```