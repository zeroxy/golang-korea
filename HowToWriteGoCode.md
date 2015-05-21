# 요약 #
  * 내용 : Go 코드 작성방법
  * 원문 : [How to Write Go Code](https://golang.org/doc/code.html)
  * 번역자 : icebluelove7@gmail.com




# Go 코드 작성방법 #

## 소개 ##

이 문서는 간단한 Go 패키지 개발 및 [go 도구](https://golang.org/cmd/go/), Go 패키지 조회, 빌드, 설치 방법, 명령어 등을 소개하고 있다.
go 도구는 특정한 방법으로 당신이 작성한 코드를 조직화할 것을 요구한다.
이 문서를 주의깊게 읽기 바란다. 이 문서는 Go를 시작하는 가장 쉬운 방법을 설명하고 있다.
[스크린 캐스트](https://www.youtube.com/watch?v=XCsL89YtqCs)를 통해서도 이 문서와 유사한 내용을 만나볼 수 있다

## 코드 조직화 ##
### 작업공간(workspace) ###

go 도구는 공공 저장소에서 관리되는 오픈 소스 코드와 함께 작업할 수 있도록 디자인되었다. 비록 당신이 작성한 코드를 오픈 소스로 만들지 않더라도, 환경을 구성하는 방식은 오픈 소스와 동일하다.

Go 코드는 반드시 작업공간(workspace) 안에 위치해야 한다. 작업공간은 세가지 루트 디렉토리로 이루어진 디렉토리 구조이다.
  * src : 패키지를 구성하는 Go 소스코드 파일들이 포함되어 있다. (1개의 패키지는 1개의 디렉토리가 할당됨)
  * pkg : 패키지 객체들이 포함됨
  * bin : 실행가능한 명령어들이 포함됨

go 도구는 소스 패키지들을 빌드하고, 결과 바이너리들을 pkg와 bin 디렉토리에 설치한다.
src 서브 디렉토리는 일반적으로 여러개의 버전 관리 저장소(Git 또는 Mercurial 같은) - 한개 또는 여러 개의 소스 패키지 개발을 관리하는 - 를 포함하고 있다.

아래 예시를 통해 작업공간이 실제로 어떠한 구조로 되어 있는지 살펴보자.
```
bin/
     hello     # 실행가능한 명령어
     outyet     # 실행가능한 명령어

pkg/
     linux_amd64/
          github.com/golang/example/
               stringutil.a           # 패키지 객체

src/
     github.com/golang/example/
          .git/      # Git 저장소 메타데이터
          hello/
               hello.go # 명령어 소스          
          outyet/
               main.go # 명령어 소스
               main_test.go # 테스트 소스
          stringutil/
               reverse.go # 패키지 소스
               reverse_test.go # 테스트 소스
```

이 작업공간은 하나의 저장소(저장소 이름은 example)와 두 개의 명령어(hello, outyet), 하나의 라이브러리(stringutil)를 포함하고 있다. 일반적인 작업공간안에는 수많은 패키지와 명령어를 포함한 여러 개의 소스 저장소가 포함되어 있을 것이다.
대부분의 Go 프로그래머들은 모든 Go 소스 코드와 관련 파일들을 하나의 작업공간에서 관리한다.
명령어와 라이브러리는 서로 다른 종류의 소스 패키지로부터 빌드된다. 이 점에 대해서는 잠시 후에 살펴보도록 하자.

### GOPATH 환경 변수 ###
GOPATH 환경 변수는 작업 환경 디렉토리 위치를 정의하는 변수이다. GOPATH 환경변수는 Go 코드를 개발할 때 거의 유일하게 설정하는 환경변수일 것이다.

우선, 작업공간 디렉토리를 생성한 다음 GOPATH를 설정한다. 당신이 원하는 디렉토리에 작업공간을 구성하라. 이 문서에서는 $HOME/go 디렉토리를 작업공간으로 사용할 것이다. 이 디렉토리는 Go 를 설치한 디렉토리와 같지 않아야 한다는 사실에 주의하라.

```
$ mkdir $HOME/go
$ export GOPATH=$HOME/go
```

편의를 위해 작업공간의 bin 서브디렉토리를 PATH에 추가한다.
```
$ export PATH=$PATH:$GOPATH/bin
```

### 패키지 경로 ###
스탠다드 라이브러리의 패키지들은 fmt, net/http 와 같이 짧은 경로를 갖는다. 당신이 직접 패키지를 만든다면, 기본 경로(base path)가 나중에 추가될 스탠다드 라이브러리의 경로나 다른 외부 라이브러리의 경로와 충돌하지 않게 설정해야 한다.

만약 시스템 안의 소스 저장소에 코드가 위치하고 있다면, 기본 경로(base path)로 소스 저장소의 루트 디렉토리를 사용해야 한다. 예를 들어, github 계정을 가진 사용자라면 github.com/user 를 기본 주소로 사용해야 한다.

소스 코드를 빌드하기 전에 원격 저장소(remote repository)에 당신의 소스 코드를 공개할 필요는 없다.
다만 공개적인 오픈 소스로 만들 것을 대비해 소스 코드를 조직화 하는 것은 좋은 습관이다.
스탠다드 라이브러리 또는 Go 에코시스템에서 유일한 것으로 구분될 수 있는 것이라면 어떠한 경로 이름이라도 사용 가능하다.

우리는 github.com/user 를 우리의 기본 경로로 사용할 것이다. 소스 코드를 관리하기 위해 작업 공간 안에 이 디렉토리를 생성하자.

```
$ mkdir -p $GOPATH/src/github.com/user
```

### 첫 번째 프로그램 작성하기 ###

간단한 프로그램을 컴파일하고 실행하기 위해 우선, 패키지 경로를 선택하고(우리는 github.com/user/hello 를 사용할 것이다) 해당 패키지 디렉토리를 당신의 작업공간 안에 생성한다.

```
$ mkdir $GOPATH/src/github.com/user/hello
```

그 다음, 새롭게 생성한 디렉토리 안에 hello.go 라는 파일을 만들고, 그 안에 아래의 Go 코드를 입력하자.

```
package main

import "fmt"

func main() {
    fmt.Printf("Hello, world.\n")
}
```

이제 go 도구를 이용해 위에서 작성한 프로그램을 빌드하고 설치할 수 있다.

```
$ go install github.com/user/hello
```

당신의 시스템 어느 곳에서든 이 명령어를 실행할 수 있다. go 도구는 GOPATH에 의해 정의된 작업공간 안에 있는
github.com/user/hello 패키지를 통해 소스 코드를 찾을 수 있다.

패키지 디렉토리로 경로를 이동한다면, 패키지 경로를 빼고 go install 명령어를 실행할 수 있다.

```
$ cd $GOPATH/src/github.com/user/hello
$ go install
```

이 명령어는 hello 명령어를 빌드하고 실행가능한 바이너리를 만들어 낸다. 이렇게 생성된 바이너리는 hello(윈도우 운영체제에서는 hello.exe)라는 이름으로 작업공간의 bin 디렉토리에 설치된다. 우리의 예제에서는
$GOPATH/bin/hello, 즉 $HOME/go/bin/hello 에 해당한다.

go 도구는 에러가 발생한 경우에만 메시지를 출력하기 때문에 명령어들이 성공적으로 실행되었을때는 어떠한 메시지도 출력되지 않는다.

이제 커맨드 라인에 전체 경로를 입력하면 프로그램을 실행할 수 있다.
```
$ $GOPATH/bin/hello
Hello, world.
```

만약 $GOPATH/bin 을 환경변수에 추가했다면 바이너리 이름만 입력해도 된다.

```
$ hello
Hello, world.
```

만약 소스 관리 시스템을 사용하는 중이라면 바로 지금이 저장소 초기화, 파일 추가, 첫번째 커밋하기에 가장 좋은 시점이다.
다시 말하지만, 이 단계는 선택사항이다. Go 코드를 작성하기 위해 소스 관리 시스템을 사용할 필요는 없다.
```
$ cd $GOPATH/src/github.com/user/hello
$ git init
Initialized empty Git repository in /home/user/go/src/github.com/user/hello/.git/
$ git add hello.go
$ git commit -m "initial commit"
[master (root-commit) 0b4507d] initial commit
 1 file changed, 1 insertion(+)
  create mode 100644 hello.go
```

원격 저장소로 코드를 푸쉬하는 작업은 독자의 몫으로 남겨두겠다.

### 첫 번째 라이브러리 작성하기 ###

라이브러리를 작성하고 hello 프로그램에서 작성한 라이브러리를 사용해보자.

첫 단계는 패키지 경로를 선택하고(우리는 github.com/user/stringutil 를 사용할 것이다), 패키지 디렉토리를 생성하는 것이다.

```
$ mkdir $GOPATH/src/github.com/user/stringutil
```

신규 생성한 디렉토리안에 reverse.go 라는 파일을 만들고 그 안에 아래의 코드를 삽입한다.

```
// Package stringutil contains utility functions for working with strings.
package stringutil

// Reverse returns its argument string reversed rune-wise left to right.
func Reverse(s string) string {
        r := []rune(s)
        for i, j := 0, len(r)-1; i < len(r)/2; i, j = i+1, j-1 {
                r[i], r[j] = r[j], r[i]
        }
        return string(r)
}
```

go build를 이용해 패키지 컴파일을 테스트한다.

```
$ go build github.com/user/stringutil
```

만약 패키지의 소스 디렉토리에 위치하고 있다면 간단히 아래와 같이 입력해도 된다.

```
$ go build
```

go build 명령어는 출력 파일을 생산하지 않는다. 출력 파일을 생산하기 위해서는 go install 을 사용해야 하며,
이 과정에서 작업공간의 pkg 디렉토리안에 패키지 객체가 생성된다.

stringutil 패키지 필드를 확인 한 뒤, 위에서 작성한 hello.go($GOPATH/src/github.com/user/hello에 위치함)를 아래와 같이 수정한다.

```
package main

import (
        "fmt"

        "github.com/user/stringutil"
)

func main() {
        fmt.Printf(stringutil.Reverse("!oG ,olleH"))
}
```

go 도구가 패키지 또는 바이너리를 설치할때마다 관련된 파일들(dependencies) 이 함께 설치된다.
때문에 아래의 명령어로 hello 프로그램을 설치하면 stringutil 패키지 또한 자동으로 설치될 것이다.

```
$ go install github.com/user/hello
```

새로운 버전의 프로그램을 실행해보면 이전과는 순서가 뒤바뀐 출력 메시지를 확인할 수 있을 것이다.

```
$ hello
Hello, Go!
```

위의 단계를 모두 마쳤다면 당신의 작업공간 구조는 아래와 같을 것이다.

```
bin/
    hello                 # 실행가능한 명령어
pkg/
    linux_amd64/          # 사용자의 운영체제와 아키텍처에 따라 다른 값이 설정된다. 
        github.com/user/
            stringutil.a  # 패키지 객체
src/
    github.com/user/
        hello/
            hello.go      # 명령어 소스
        stringutil/
            reverse.go    # 패키지 소스
```

go install 은 stringutil.a 객체를 pkg/linux\_amd64 디렉토리 안에 위치시킨다. 또한, pkg/linux\_amd64 디렉토리는 소스 디렉터리의 구조를 그대로 반영하고 있다. 이런 구조를 갖는 이유는 나중에 go 도구를 실행할 때 패키지 객체를 찾는데 도움을 주고, 불필요한 패키지 재컴파일을 피하기 위함이다. linux\_amd64 부분은 크로스 컴파일을 지원하는데 활용되며,
당신 시스템의 운영체제와 아키텍쳐를 반영할 것이다.

실행가능한 Go 명령어는 정적으로 링크되어 있다. 따라서, Go 프로그램을 실행하기 위해 패키지 객체들을 따로 제공할 필요는 없다.

### 패키지 이름 ###
Go 소스 파일의 첫 번째 문장은 반드시 package name 으로 시작해야 한다.
name은 import되는 패키지의 기본 이름이다. (패지키 안의 모든 파일들은 같은 이름을 사용해야 한다.)
GO는 import 경로의 마지막 요소를 패키지 이름으로 사용한다.
crypto/rot13 로 import되는 패키지는 rot13 이라는 이름으로 사용해야 한다.
실행가능한 명령어들은 반드시  package main 을 사용해야 한다.
하나의 바이너리안에 링크되는 모든 패키지들 사이에서 구별될 수 있는 이름이며, import 경로(파일 이름 전체)가 다른 경로 구별되는 것이라면 어떠한 패키지 이름이라도 사용가능하다.
Go의 이름 명명 규칙에 대해 자세히 알고 싶다면 [Effective Go](https://golang.org/doc/effective_go.html#names)
문서를 참조하라.

## 테스팅 ##
Go는 go test 명령어와 testing 패키지로 구성된 가벼운 테스트 프레임워크를 가지고 있다.
테스트는 파일 이름이 `_test.go`로 끝나야 하며, `func (t *testing.T)` 시그네쳐를 갖는 TestXXX 라는 이름의 함수를 포함하고 있어야 한다. 테스트 프레임워크는 각각의 함수를 실행한다. 만약 함수가 t.Error 또는 t.Fail와 같은 실패 함수(failure function)를 호출한다면, 테스트는 실패로 여겨질 것이다.

$GOPATH/src/github.com/user/stringutil/reverse\_test.go 소스코드를 수정하여 stringutil 패키지에 test를 추가해보자.

```
package stringutil

import "testing"

func TestReverse(t *testing.T) {
        cases := []struct {
                in, want string
        }{
                {"Hello, world", "dlrow ,olleH"},
                {"Hello, 世界", "界世 ,olleH"},
                {"", ""},
        }
        for _, c := range cases {
                got := Reverse(c.in)
                if got != c.want {
                        t.Errorf("Reverse(%q) == %q, want %q", c.in, got, c.want)
                }
        }
}
```

go test를 이용해 테스트를 실행해보자.

```
$ go test github.com/user/stringutil
ok      github.com/user/stringutil 0.165s
```

패키지 디렉토리에서 go 도구를 실행한다면 아래의 명령어만 입력해도 된다.
```
$ go test
ok      github.com/user/stringutil 0.165s
```

go help test를 실행해보고, 더 자세한 내용은 [테스팅 패키지 문서](https://golang.org/pkg/testing/)를 통해서 확인하자.

## 원격 패키지 ##
Git 나 Mercurial 같은 소스 버전 관리 시스템을 사용해 패키지 소스 코드를 어떻게 얻을 수 있는지 import 경로에 정의할 수 있다. go 도구는 이러한 특성을 원격 저장소로부터 자동적으로 패키지를 조회하는데 사용한다. 예를 들어,
이 문서에서 사용한 예제는 [Git 저장소](https://github.com/golang/example)에도 존재한다. 만약 패키지 import 경로에
저장소를 포함하고 싶다면 조회, 빌드, 설치를 자동으로 처리해주는 go get 을 이용하라.

```
$ go get github.com/golang/example/hello
$ $GOPATH/bin/hello
Hello, Go examples!
```

만약 추가한 패키지가 작업공간에 나타나지 않는다면 go get는 GOPATH에 의해 정의된 첫번째 작업공간 안에 해당 패키지를 위치시킬 것이다. (만약 패키지가 이미 존재한다면, go get은 원격 fetch를 건너뛰고 go install 과 동일하게 동작할 것이다.)

go get 명령어를 실행한 이후, 작업공간 디렉토리 구조는 다음과 같을 것이다.

```
bin/
    hello                           # 실행가능한 명령어
pkg/
    linux_amd64/
        github.com/golang/example/
            stringutil.a            # 패키지 객체
        github.com/user/
            stringutil.a            # 패키지 객체
src/
    github.com/golang/example/
        .git/                       # Git 저장소 메타데이터
        hello/
            hello.go                # 명령어 소스
        stringutil/
            reverse.go              # 패키지 소스
            reverse_test.go         # 테스트 소스
    github.com/user/
        hello/
            hello.go                # command source
        stringutil/
            reverse.go              # package source
            reverse_test.go         # test source
```

github에 위치한 hello 명령어는 같은 저장소에 있는 stringutil 패키지와 의존성(depends on)이 있다. hello.go 파일에 사용된 import 구문은 위에서 사용한 import 구문과 동일한 구문을 사용하기 때문에 go get 명령어를 통해 hello.go와 관련된 패키지 설치가 가능하다.

```
import "github.com/golang/example/stringutil"
```

이러한 문법 구조(convention)는 다른 사용자들이 당신의 Go 패키지들을 쉽게 사용할 수 있게 만든다.
[Go Wiki](https://golang.org/wiki/Projects), [godoc.org](https://godoc.org/)는 외부 Go 프로젝트 목록을 제공한다.

go 도구로 원격 저장소 사용하기에 대한 더 자세한 정보를 원한다면, [go help importpath](https://golang.org/cmd/go/#hdr-Remote_import_paths)를 참조하라.


## 추가 정보 ##
  * 새로운 Go 버전 출시 소식을 알고 싶다면, [golang 관련 메일링리스트](https://groups.google.com/group/golang-announce)를 구독하라.
  * 깔끔하고, 문법에 맞는 Go 코드 작성하기에 대한 팁을 얻고 싶다면 [Effective Go](https://golang.org/doc/effective_go.html)를 참조하라.
  * Go 언어를 단계별로 학습하고 싶다면 [Go 투어](https://tour.golang.org/)페이지  를 참조하라.
  * [Go 문서 및 관련 글 페이지](https://golang.org/doc/#articles)에서는 Go 언어, 라이브러리, 도구들에 관한 심도있는 글들을 만나볼 수 있다.

## 도움받기 ##
  * [Freenode IRC 서버](http://freenode.net/)의 go-nuts 채널을 통해 고퍼들로부터 실시간 도움을 받을 수 있다.
  * Go 언어 관련 토론을 위한 공식 메일링 리스트는 [Go Nuts](http://groups.google.com/group/golang-nuts) 이다.
  * 버그 신고는 [Go issue tracker](http://golang.org/issue)를 이용하라.