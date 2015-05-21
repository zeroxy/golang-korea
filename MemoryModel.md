# 요약 #
  * 내용 : 메모리 모델
  * 원문 : [The Go Memory Model](http://golang.org/doc/go_mem.html)
  * 번역자 : jeyong, Jooyung.Han




# 메모리 모델 #

## 소개 ##
Go 메모리 모델에서는 한 goroutine이 어떤 변수에 쓴 값을 다른 goroutine이 해당 변수의 갱신된 값을 제대로 읽는 것을 보장할 수 있는 상황을 명시한다.

## 이전에 발생(Happens Before) ##
단일 goroutine내에서 읽기/쓰기는 프로그램에서 지정한 순서대로 실행되는 것처럼 동작해야만 한다. 즉 컴파일러와 프로세서는 단일 goroutine내에서 실행되는 읽기/쓰기 순서를 조정할 수 있는데 이는 go언어 스펙에서 정의한 goroutine내의 동작을 변경시키지 않을 경우에만 해당된다. 순서 재조정 때문에 한쪽 goroutine에서의 실행 순서가 다른 goroutine에서 알고 있는 순서와 달리질 수도 있다. 예를 들면 하나의 goroutine이 `a=1; b=2;`를 실행한다면 다른 goroutine은 a값을 갱신하기 전에 b의 갱신된 값을 보게 될 수도 있다.

읽기/쓰기의 요구사항을 구체화하기 위해 Go 프로그램에서 메모리 연산의 실행에서 일부 순서에 대해서 '이전에 발생'(happens before)을 정의한다. 이벤트 e1은 이벤트 e2전에 일어난다면 e2는 e1후에 일어난다고 말한다. 또 e1이 e2전에 일어나지 않고 e2 후에 일어나지 않는다면 e1과 e2는 동시에(concurrently) 발생한다고 말한다.

단일 goroutine내에서 이전에 일어나는 순서는 프로그램으로 그 순서가 표현된다.

다음 2가지가 지켜진다면 변수 v에 대한 읽기 r은 v에 대한 쓰기 w을 관찰하는 것이 _가능\_해 진다.
  1. w는 r전에 발생한다.
  1. w이후 r전에 일어나는 v에 대한 또다른 w'가 없어야 한다._

변수 v의 읽기 r이 쓰기 w를 관찰하는 것을 보장하려면 w가 r이 관찰할 수 있는 유일한 것이어야 한다. 즉, 읽기 r은 다음 두 가지가 모두 지켜질 때 쓰기 w를 관찰하는 것으로 _보장\_된다.
  1. w는 r전에 발생한다.
  1. 공유된 변수 v에 대한 다른 쓰기들이 w이전 혹은 r이후에 일어난다._

이 두가지 조건은 첫번째 두 조건보다 강력하다. 다른 쓰기 동작이 w나 r과 동시에(concurrently) 발생해서도 안되기 때문이다.

단일 goroutine내에서라면 동시성 문제가 없기 때문에 두가지 정의가 동등하다. 즉 읽기 r은 가장 최근의 쓰기 w에 의해 갱신된 값을 읽어들인다. 여러 개의 goroutine이 변수 v를 공유하여 접근하는 경우라면 원하는 쓰기 값을 읽어들이는 것을 보장하기 위해서는 '이전에 발생' 조건을 성립시켜주는 동기화 이벤트를 사용해야만 한다.

변수 v가 해당 타입의 0값으로 초기화되는 것도 메모리 모델에서는 쓰기로 간주된다.

해당 머신의 워드 단위보다 큰 읽기와 쓰기는 여러번에 걸친 워드크기의 쓰기 동작이 일어나는 것과 같다. 다만 그 순서는 명시되지 않는다.


## 동기화(Synchronization) ##


### 초기화(Initialization) ###

프로그램 초기화는 단일 goroutine에서 실행되고 초기화 동안 생성된 새로운 goroutine은 초기화가 끝날 때까지 실행을 시작하지 않는다.
패키지 p가 패키지 q를 import하면 q의 init함수의 완료는 p가 시작되기 전에 일어난다.
함수 main.main의 시작은 모든 init함수들이 끝나고나서 일어난다.
모든 init함수들이 완료된 후에 init 함수들 중에 생성된 goroutine의 실행이 일어난다.

### Goroutine 생성 ###
새로운 goroutine을 시작시키는 `go` 구문은 해당 goroutine이 시작되기 전에 일어난다.

예를들어, 이 프로그램에서
```
var a string

func f() {
	print(a)
}

func hello() {
	a = "hello, world"
	go f()
}
```
`hello`를 호출하면 미래의 어느 시점에 "hello, world"를 출력한다.(아마 `hello`가 리턴된 이후가 될 것이다.)

### goroutine 소멸 ###

goroutine의 종료는 프로그램에서 특정 이벤트 전에 일어나는 것을 보장하지 못한다. 예를들 이 프로그램에서는:
```
var a string

func hello() {
	go func() { a = "hello" }()
	print(a)
}
```
`a`에 값을 할당 후에 특정 동기화 이벤트가 없어서 다른 goroutine에서 `a`의 값이 갱신된 것을 읽어들일 것으로 보장할 수 없다. 사실 공격적인 컴파일러는 전체 `go` 구문을 지울수도 있다.

만약 goroutine이 미치는 효과가 다른 goroutine에서 관찰할 수 있어야 하는 경우라면 상대적인 순서를 정하기 위해 락(lock)이나 채널(channel) 통신과 같은 동기화 메커니즘을 사용한다.

### 채널 통신(Channel communication) ###

채널 통신은 goroutine들 사이에서 동기화를 달성하는 주요 방법이다. 특정 채널에서 각 전송은 해당 채널로부터 대응되는 수신(주로 다른 goroutine에서)과 일대일로 매치된다.

특정 채널에서 전송은 해당 채널로부터 대응하는 수신이 완료되기 전에 일어난다.

아래 프로그램
```
var c = make(chan int, 10)
var a string

func f() {
	a = "hello, world"
	c <- 0
}

func main() {
	go f()
	<-c
	print(a)
}
```
`"hello, world"` 출력을 보장한다. 변수 `a`에 쓰기는 `c`로 전송 전에 일어나며, `c`로의 전송은 `c`에 대응되는 수신이 완료되기 전에 발생한다. 다시 `c`로부터의 수신은 `print`전에 일어난다.

버퍼없는 채널(unbuffered channel)로부터 수신은 해당 채널로의 전송이 완료되기 전에 일어난다.

아래 프로그램은(위와 같지만, 발신과 수신 구문이 바뀌었고 버퍼없는 채널을 사용했다.)
```
var c = make(chan int)
var a string

func f() {
	a = "hello, world"
	<-c
}
```

```
func main() {
	go f()
	c <- 0
	print(a)
}
```
역시 `"hello, world"` 출력을 보장한다. 변수 `a`에 쓰기는 `c`에서 수신하기 전에 발생하고, `c`에서의 수신은 대응되는 발신이 끝나기 전에 일어난다. `c`로의 발신은 `print`전에 발생한다.

만약 채널이 버퍼를 가지고 있다면(예를 들어, `c = make(chan int, 1)`처럼) 프로그램은 `"hello, world"`의 출력을 보장하지 않는다.(아마도 빈 문자열을 출력할 수도 있다. 그렇다고 해서 `"goodbye, universe"`를 출력할 리는 없으며 또한 시스템이 다운되지는 않는다.)

### Locks ###

`sync` 패키지에는 `sync.Mutex`와 `sync.RWMutex`의 두 가지 lock 타입이 구현되어 있다.

`sync.Mutex`나 `sync.RWMutex` 타입의 변수 `l`이 있고, `n < m`인 조건에서는 `n`번째 `l.Unlock()` 호출이 `m`번째 `l.Lock()`호출이 반환되기 전에 일어난다.

아래 프로그램을 보자.
```
var l sync.Mutex
var a string

func f() {
	a = "hello, world"
	l.Unlock()
}

func main() {
	l.Lock()
	go f()
	l.Lock()
	print(a)
}
```

이 프로그램은 `"hello, world"`가 출력되는 것을 보장한다. `f`함수에서 `l.Unlock()`을 처음(1번째) 호출한 것이 `main` 함수에서 2번째로 호출한 `l.Lock()`이 반환되기 전에 일어나며, 따라서 `print`보다 먼저 실행된다.

`sync.RWMutex` 변수 `l`에 대한 `l.RLock` 호출은 다음을 만족하는 n이 있어야 한다. _n\_번째 `l.Unlock` 호출 이후에 `l.RLock`이 반환되며,_n+1\_번째 `l.Lock`호출 이전에 이에 매치되는 `l.RUnlock` 호출이 있다.

(참고. `RLock` 호출이 반환되려면 `Unlock`이 호출되어야 하며, `RLock`으로 읽기 Lock이 잠긴 상태에서는 다음 번 `Lock` 호출이 반환하려면 `RUnlock`이 호출되어야 한다는 의미)


### `Once` ###

`sync` 패키지는 여러 개의 goroutine이 있을 때 `Once` 타입을 사용하여 안전하게 초기화할 수 있는 방법을 제공한다. 여러 개의 쓰레드가 `f`에 대해 여러번 `once.Do(f)`를 호출해도 결과적으로 `f`는 한번만 실행되며, `f()`가 반환할 때까지 블록된다.

`once.Do(f)`로 실행되는 `f()`는 `once.Do(f)`가 반환되기 전에 발생(반환)한다.

아래 프로그램을 보자.
```
var a string
var once sync.Once

func setup() {
	a = "hello, world"
}

func doprint() {
	once.Do(setup)
	print(a)
}

func twoprint() {
	go doprint()
	go doprint()
}
```

`twoprint`를 호출하면 `"hello, world"`가 두 번 출력된다. 처음 호출된 `twoprint`에 의해 `setup`은 한번만 실행된다.


## 부정확한 동기화(Incorrect synchronization) ##

읽기 r이 쓰기 w와 동시에(concurrently) 일어나면 w에 의해 갱신된 값을 읽을 수도 있다는 점을 놓치지 말아라. 설령 갱신된 값을 읽는다 하더라도 쓰기 w보다 먼저 발생한 쓰기 결과들을 읽을 수 있다는 것을 의미하지는 않는다.

아래 프로그램을 보자.
```
var a, b int

func f() {
	a = 1
	b = 2
}

func g() {
	print(b)
	print(a)
}

func main() {
	go f()
	g()
}
```

`g`는 `2`와 `0`을 출력할 수도 있다.

이 사실은 몇 가지 널리 알려진 이디엄들을 무효화시킨다.

Double-checked locking은 동기화 비용을 낮추기 위한 방법이다. 예를 들어 `twoprint` 프로그램을 다음과 같이 작성할 수도 있는데 이는 잘못된 것이다.

```
var a string
var done bool

func setup() {
	a = "hello, world"
	done = true
}

func doprint() {
	if !done {
		once.Do(setup)
	}
	print(a)
}

func twoprint() {
	go doprint()
	go doprint()
}
```

이 프로그램에서 `doprint`을 보면, `done` 변수에 값이 쓰여진 것을 확인했다고 하여 `a`에 쓰여진 값을 읽을 수 있다는 것은 보장할 수 없다. 이 프로그램은 `"hello, world"` 대신에 빈 문자열을 출력할 수도 있다.

busy waiting 은 어떤 값이 바뀌는지를 반복문을 돌면서 확인하는 방법이다. 이것도 옳지 못한 이디엄이 된다.
```
var a string
var done bool

func setup() {
	a = "hello, world"
	done = true
}

func main() {
	go setup()
	for !done {
	}
	print(a)
}
```
앞서의 예와 마찬가지로 이 프로그램에서도 `main`에서 `done`변수의 값이 바뀌는 것을 확인했다고 하여 `a`변수의 바뀐 값을 읽을 수 있다는 것은 보장되지 못한다. 빈 문자열을 출력할 수도 있다는 얘기다. 심지어 `done`이 갱신되는 것을 `main`에서 전혀 알아차릴 수 없는 경우도 있다. 두 쓰레드 간에 아무런 동기화 이벤트가 없기 때문이다. `main`함수의 반복문은 결국 끝날 것이라고 보장할 수 없다.

이 문제를 약간 변형해보면 다음과 같은 프로그램을 생각해볼 수 있다.

```
type T struct {
	msg string
}

var g *T

func setup() {
	t := new(T)
	t.msg = "hello, world"
	g = t
}

func main() {
	go setup()
	for g == nil {
	}
	print(g.msg)
}
```

`main`에서 `g != nil`을 감지하여 반복문을 빠져나오더라도 `g.msg` 값이 아직 초기화되지 않았을 수 있다.

이상의 예제들에서 의도하는 해법은 모두 같다. 명시적 동기화를 사용하라!