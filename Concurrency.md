
# 요약 #
  * 다른 언어와 구별되며 Go의 가장 두드러진 특징이 바로 Concurrency이다. Go를 만든 사람들이 제안하는 Concurrency의 개념과 사용법을 한 곳에 모아 정리한다.

# 관련 자료 #
## Real World Go ##
  * Concurrency: synchronization
    * Look back at the sort example - how to tell when it’s done?
    * Use a channel to synchronize goroutines:
```
done := make(chan bool)
doSort := func(s []int) {
    sort(s)
    done <- true
}

i := pivot(s)
go doSort(s[:i])
go doSort(s[i:])
<-done
<-done
```
    * Unbuffered channel operations are synchronous;
    * the send/receive happens only when both sides are ready.

---

  * Concurrency: communication
    * A common task: many workers feeding from task pool.
    * Traditionally, worker threads contend over a lock for work:
```
type Task struct {
    // some state
}
type Pool struct {
    Mu sync.Mutex
    Tasks []Task
}
func worker(pool *Pool) { // many of these run concurrently
    for {
        pool.Mu.Lock()
        task := pool.Tasks[0]
        pool.Tasks = pool.Tasks[1:]
        pool.mu.Unlock()
        process(task)
    }
}
```

---

  * Concurrency: communication
    * A Go idiom: many worker goroutines receive tasks from a channel.
```
type Task struct {
    // some state
}
func worker(in, out chan *Task) {
    for {
        t := <-in
        process(t)
        out <- t
    }
}
func main() {
    pending, done := make(chan *Task), make(chan *Task)
    go sendWork(pending)
    for i := 0; i < 10; i++ {
        go worker(pending, done)
    }
    consumeWork(done)
}
```

---

## Go Tech Talk ##
  * A multiplexed server
```
type Request struct {
    a, b int;
    replyc chan int; // reply channel inside the Request
}
type binOp func(a, b int) int
func run(op binOp, req *Request) {
    req.replyc <- op(req.a, req.b)
}

func server(op binOp, service chan *Request) {
    for {
        req := <-service; // requests arrive here
        go run(op, req); // don't wait for op
    }
}

func StartServer(op binOp) chan *Request {
    reqChan := make(chan *request);
    go server(op, reqChan);
    return reqChan;
}
```

---

  * The client
```
// Start server; receive a channel on which
// to send requests.
server := StartServer(
          func(a, b int) int {return a+b});
// Create requests
req1 := &Request{23,45, make(chan int)};
req2 := &Request{-17,1<<4, make(chan int)};
// Send them in arbitrary order
server <- req1; server <- req2;
// Wait for the answers in arbitrary order
fmt.Printf("Answer2: %d\n", <-req2.replyc);
fmt.Printf("Answer1: %d\n", <-req1.replyc);
```

---

  * Select
    * A select is like a switch statement in which the cases are communications. A simple example uses a second channel to tear down the server.
```
func server(op binOp, service chan *Request,
            quit chan bool) {
    for {
        select {
        case req := <-service:
            go run(op, req); // don't wait
        case <-quit:
            return;
        }
    }
}
```

---

  * Chaining
```
package main
import ("flag"; "fmt")
var ngoroutine = flag.Int("n", 100000, "how many")
func f(left, right chan int) { left <- 1 + <-right }
func main() {
    flag.Parse();
    leftmost := make(chan int);
    var left, right chan int = nil, leftmost;
    for i := 0; i < *ngoroutine; i++ {
        left, right = right, make(chan int);
        go f(left, right);
    }
    right <- 0; // bang!
    x := <-leftmost; // wait for completion
    fmt.Println(x); // 100000
}
```

---

## Expressiveness Of Go ##
  * A simple worker pool
    * A unit of work:
```
type Work struct { x, y, z int }
```
    * A worker task:
```
func worker(in <-chan *Work, out chan <- *Work) {
    for w := range in {
        w.z = w.x * w.y
        out <- w
    }
}
```

  * Driver:
```
func Run() {
    in, out := make(chan *Work), make(chan *Work)
    for i := 0; i < 10; i++ { go worker(in, out) }
    go sendLotsOfWork(in)
    receiveLotsOfResults(out)
}
```
  * No low-level synchronization needed.

---

  * Closures
    * Closures are just local functions
```
func Compose(f, g func(x float) float)
            func(x float) float {
    return func(x float) float {
        return f(g(x))
    }
}

func main() {
    print(Compose(sin, cos)(0.5))
}
```
    * Fit easily into implementation since local variables already move to heap when necessary.

---

  * Closures and concurrency
    * Query servers in replicated database, return first response.
```
func Query(conns []Conn, query string) Result {
    ch := make(chan Result, 1) // buffer of 1 item
    for _, conn := range conns {
        go func(c Conn) {
            _ = ch <- c.DoQuery(query)
        }(conn)
    }
    return <-ch
}
```

---

## Another Go at Language ##
  * A worker pool
    * Traditional approach (C++, etc.) is to communicate by sharing memory and protecting the shared data structures with mutexes (locks).
    * Server would use shared memory to apportion work:
```
type Work struct {
   x, y, z int
   assigned, done bool
}
type WorkSet struct {
   mu sync.Mutex
   work []*Work
}
```
  * But not in Go.

---

  * Share memory by communicating
    * In Go, you reverse the equation. Channels use <- operator to synchronize and communicate.Typically don't need or want mutexes.
```
type Work struct { x, y, z int }
func worker(in <-chan *Work, out chan<- *Work) {
   for w := range in {
      w.z = w.x * w.y  // do some work...
      out <- w
   }   
}
func main() {
   in, out := make(chan *Work), make(chan *Work)
   for i := 0; i < 10; i++ { go worker(in, out) }
   go sendLotsOfWork(in)
   receiveLotsOfResults(out)
}
```

---

## Go Emerging Languages Conference Talk ##
  * A card reassembly example in Go
```
func copy(west, east chan byte) { for { east <- <-west } }
func assemble(X chan byte, printer chan []byte) {
    lineimage := make([]byte, 125)
    for i := 0;; {
        lineimage[i] = <-X
        if i < 124 { i++ } else { printer <- lineimage; i = 0 }
    }
}

func disassemble(cardfile chan []byte, X chan byte) {
    for {
        cardimage := <-cardfile
        i := 0
        for i < len(cardimage) { X <- cardimage[i]; i++ }
        for i < 80 { X <- ' '; i++ }
    }
}

go disassemble(cardreader, chars1)
go copy(chars1, chars2)
go assemble(chars2, lineprinter)
```