# 요약 #
  * 내용 : 소스 파일로 Go 설치하기
  * 원문 : [Installing Go from source](https://golang.org/doc/install/source)
  * 번역자 : icebluelove7@gmail.com



# 서 론 #

Go는 [BSD 스타일 라이센스](https://golang.org/LICENSE)  하에서 배포되는 오픈소스 프로젝트이다. 이 문서는 Go 소스파일을 확인하는 방법과 시스템에 설치하고 실행하는 법에 대해 설명하고 있다.

대부분의 사용자들이 소스 파일로부터 직접 설치할 필요는 없으며,
[Go 시작하기](https://code.google.com/p/golang-korea/wiki/GoInstall)에서 언급한 바이너리 패키지로부터 설치하는
훨씬 더 간단한 방법을 이용해 Go를 설치할 것이다. 하지만 미리 컴파일된 바이너리 패키지 개발을 돕고 싶다면
이 문서를 계속 읽어라.

2개의 공식적인 Go 컴파일러 도구가 있다. 이 문서는 _gc_ Go 컴파일러와 도구들(6g, 8g 등)에 중점을 두고 작성되었다.
GCC 백 엔드를 사용한 좀더 전통적인 컴파일러인 gccgo가 어떻게 동작하는지에 대한 정보를 알고 싶다면,
[gccgo 설정과 사용](https://golang.org/doc/install/gccgo) 문서를 참조하기 바란다.

Go 컴파일러들은 3가지 명령어 셋을 지원한다. 다른 아키텍처 환경에서 동작하는 컴파일러들의 품질에는
큰 차이가 있다.

amd64 (x86 64비트); 6g,6l,6c,6a
<blockquote>안정적인 구현. 컴파일러는 효과적인 옵티마이저(레지스터라이져)를 가지고 있으며, 훌륭한 코드를 만들어 낸다. (가끔씩 gccgo가 훨씬 나은 경우도 있다.)</blockquote>

386 (x86 또는 x86 32비트); 8g,8l,8c,8a
<blockquote>
amd64와 비슷한 성능<br>
<blockquote></blockquote>
arm (a.k.a. ARM); 5g,5l,5c,5a<br>
<blockquote>
리눅스, FreeBSD, NetBSD 바이너리 지원. 다른 포트들에 비해 광범위하게 사용되지 않는다. </blockquote></blockquote>

저 수준(low-level)의 운영체제 인터페이스 코드 같은 것들을 제외하고, 모든 아키텍처 환경에서 런타임 지원이 제공된다. 또한, 컴파일러는 표시하고 쓸기(mark-and-sweep) 가비지 콜렉터가 포함되어 있으며, 효율적인
배열과 문자열 슬라이싱, 요청에 따라 늘어났다 줄어들었다 하는 스택과 같은 효율적인 고루틴(goroutine, 역자주 : 다른 함수들과 함께 동시에 실행할 수 있는 함수)을 지원한다.

컴파일러들은 dragonFly, BSD, FreeBSD, 리눅스, NetBSD, OpenBSD, OS X(다윈), Plan 9, 솔라리스, 윈도우 운영체제를 타겟으로 할 수 있다. 지원 가능한 조합 목록은 아래의 [환경 변수에 대한 논의](https://code.google.com/p/golang-korea/wiki/InstallingGoFromSource#또다른_환경_변수들)에 명시되어 있다.

# 필요할 경우, C 도구 설치하기 #
Go 도구 체인은 C로 작성되어 있다. 이들을 빌드하기 위해서는 C 컴파일러가 설치되어 있어야 한다.
운영체제별 명령어는 Go 커뮤니티의 [소스파일로부터 설치하기](https://golang.org/wiki/InstallFromSource#install-c-tools)페이지를 참조하라.

# 필요할 경우 Git 설치하기 #
다음 단계를 진행하기 위해서는 Git가 설치되어 있어야 한다. (다음 단계로 넘어가기 전에 git 명령어가 당신의 시스템에 존재하는지 확인하라.) 만약 Git 가 설치되어 있지 않다면, [Git 다운로드 페이지](http://git-scm.com/downloads)의 명령에 따라 설치하면 된다.

# 저장소 내려받기(Fetch the repository) #
Go는 go 디렉토리에 설치된다. 상위 디렉토리로 이동하여 go 디렉토리가 이미 존재하고 있는 것은 아닌지 반드시 확인하라. 그 다음 저장소 복사본을 만들고(clone) 최신 릴리즈 버전에 대한 태그를 확인한다 :

```
$ git clone https://go.googlesource.com/go
$ cd go
$ git checkout go1.4.1
```

# (선택사항) 마스터 브랜치로 변경하기 #
만약 go 소스코드를 수정하고, [프로젝트에 당신이 수정한 내용을 반영](https://golang.org/doc/contribute.html)하고 싶다면, release 브랜치가 아닌 master(development) 브랜치로 이동해야 한다. 이러한 경우가 아니라면, 이 단계는 건너뛰어라.

```
$ git checkout master
```

# Go 설치하기 #
Go 배포판을 빌드하기 위해 아래 명령어를 실행한다.
```
$ cd go/src 
$ ./all.bash
```

(윈도우 운영체제에서는 all.bat 를 사용하여 빌드하라.)

설치가 제대로 되었다면 마지막에 아래와 같은 메시지가 나타날 것이다.

```
ALL TESTS PASSED

---
Installed Go for linux/amd64 in /home/you/go.
Installed commands in /home/you/go/bin.
*** You need to add /home/you/go/bin to your $PATH. ***
```

메시지의 마지막 부분에는 운영체제, 아키텍처, 설치된 루트 디렉토리에 대한 정보가 나타난다.

빌드 과정을 조정하는 방법에 대한 정보는 아래의  [환경 변수에 대한 논의](https://code.google.com/p/golang-korea/wiki/InstallingGoFromSource#또다른_환경_변수들)를 참조하라. all.bash(또는 all.bat)는 Go에 대한 중요한 테스트들을 실행하며, 단순히 Go를 빌딩하는 것에 비해 좀 더 시간이 걸린다. 만약 이러한 테스트들을 실행하고 싶지 않다면 make.bash(또는 make.bat)를 사용하라.

# 설치 확인 #
간단한 프로그램을 빌드하여 Go가 제대로 설치되었는지 확인해보자.
_hello.go_ 라는 파일을 새로 만들고, 아래의 코드를 삽입한다.

```
package main

import "fmt"

func main() {
    fmt.Printf("hello, world\n")
}
```

그 다음, go 툴을 이용해 아래의 명령어를 실행한다.

```
$ go run hello.go
hello, world
```

hello, world 메시지가 나타난다면 Go 가 제대로 설치된 것이다.

# 작업 환경 설정 #
이제 거의 완료되었다. 환경 설정을 해보자.

[Go Code 작성하기](https://code.google.com/p/golang-korea/wiki/HowToWriteGoCode)를 참조하여 Go 도구들을 사용하기 위해 필요한 명령어들을 익혀보자.

# 추가적인 도구(tools) 설치 #
[godoc](https://golang.org/cmd/godoc/) 및 여러가지 Go 도구들에 대한 소스코드는 [go.tools 저장소](https://golang.org/x/tools)에 있다. 이들 모두를 설치하기 위해서는, go get 명령어를 실행하면 된다.

```
$ go get golang.org/x/tools/cmd/...
```

특정 도구만 따로 설치할 수 있다(예를 들어 godoc)

```
$ go get golang.org/x/tools/cmd/godoc
```

이러한 도구들을 설치하기 위해서는, go get 명령어가 필요하다.
(go get 명령어를 사용하기 위해서는 로컬 시스템에 Git 가 설치되어 있어야 한다.)

또한 작업 환경인 GOPATH 를 설정해야 한다. 자세한 사항은 [Go Code 작성하기](https://code.google.com/p/golang-korea/wiki/HowToWriteGoCode)를 참조하라.

주의 : go 명령어는 godoc 바이너리를 $GOROOT/bin (또는 $GOBIN) 에 설치한다.
cover와 vet 바이너리는 $GOROOT/pkg/tool/$GOOS/$GOARCH 에 설치될 것이다.
cover, vet 명령어는 go tool cover, go tool vet 의 형태로 사용할 수 있다.

# 커뮤니티 리소스 #
대표적인 커뮤니티인 [Freenod](http://freenode.net/) IRC 서버의 go-nuts 채널과 [Go Nuts](https://groups.google.com/group/golang-nuts) 메일링 리스트에는
개발과 설치과정에서 어려움을 겪는 사용자들에게 도움을 줄 수 있는 개발자들이 열심히 활동하고 있다.
최신 정보를 얻고자 한다면, [golang-checkins 메일링 리스트](https://groups.google.com/group/golang-checkins)를 활용할 수 있다.
이곳에서는 Go 저장소에서 진행되는 작업들에 대한 요약정보를 확인할 수 있다.

버그 신고는 [Go issue tracker](https://golang.org/issue/new)를 이용하라.

# 최신 버전 유지하기 #
새로운 릴리즈 버전은 [golang-announce 메일링 리스트](https://groups.google.com/group/golang-announce)에서
발표된다. 각각의 발표 정보에는 최신 릴리즈에 대한 태그가 포함되어 있다. (예. go1.4)

로컬 시스템에 설치된 트리를 최신 버전으로 업데이트 하기 위해서는 아래의 명령어를 실행하라:

```
$ cd go/src
$ git fetch
$ git checkout <tag>
$ ./all.bash
```

tag에는 릴리즈 버전을 입력하면 된다.

# 또다른 환경 변수들 #
환경변수를 통해 사용자에 최적화된 Go 컴파일 환경을 구축할 수 있다. 빌드할 때는 따로 환경 변수 설정이 필요하지 않다.
사용자는 기본 환경 변수값에 새로운 값을 할당할 수 있다.

  * $GOROOT
<blockquote>
Go 트리의 루트이며, 주로 $HOME/go 로 설정된다. 이 환경 변수 값은 컴파일 될때 설정되며, all.bash 가 실행될 때 디렉토리의 루트의 기본값으로 사용된다. 저장소로부터 여러 개의 로컬 복사본을 유지하고 있는 경우를 제외하고는 이 값을 따로 설정할 필요없다.<br>
</blockquote>

  * $GOROOT\_FINAL
<blockquote>
$GOROOT가 명시적으로 설정되어 있지 않을 때, 설치된 바이너리와 스크립트에서 사용한다. 기본값은 $GOROOT의 값이다.<br>
만약 Go 트리를 한 장소에서 빌드한 이후, 다른 곳으로 옮기려 할때 최종 장소를 $GOROOT_FINAL을 이용해 설정한다.<br>
</blockquote>

  * $GOOS, $GOARCH
<blockquote>
타켓 운영체제와 컴파일 아키텍처 이름. 기본값은 $GOHOSTOS와 $GOHOSTARCH 값이다.(아래 참조)<br>
$GOOS에 설정될 수 있는 값은 darwin(맥 OS X 10.6 이상), dragonfly, freebsd, 리눅스, netbsd, openbsd, plan9, 솔라리스, 윈도우가 있다. $GOARCH에 설정될 수 있는 값은 amd64(64비트 x86), 386(32비트 x86), arm(32비트 ARM)이 있다.<br>
$GOOS와 $GOARCH 에서 사용될 수 있는 조합은 다음과 같다 :</li></ul>

<table><thead><th> <b>$GOOS</b> </th><th> <b>$GOARCH</b> </th></thead><tbody>
<tr><td> darwin       </td><td> 386            </td></tr>
<tr><td> darwin       </td><td> amd64          </td></tr>
<tr><td> dragonfly    </td><td> 386            </td></tr>
<tr><td> dragonfly    </td><td> amd64          </td></tr>
<tr><td> freebsd      </td><td> 386            </td></tr>
<tr><td> freebsd      </td><td> amd64          </td></tr>
<tr><td> freebsd      </td><td> arm            </td></tr>
<tr><td> linux        </td><td> 386            </td></tr>
<tr><td> linux        </td><td> amd64          </td></tr>
<tr><td> linux        </td><td> arm            </td></tr>
<tr><td> netbsd       </td><td> 386            </td></tr>
<tr><td> netbsd       </td><td> amd64          </td></tr>
<tr><td> netbsd       </td><td> arm            </td></tr>
<tr><td> openbsd      </td><td> 386            </td></tr>
<tr><td> openbsd      </td><td> amd64          </td></tr>
<tr><td> plan9        </td><td> 386            </td></tr>
<tr><td> plan9        </td><td> amd64          </td></tr>
<tr><td> solaris      </td><td> amd64          </td></tr>
<tr><td> windows      </td><td> 386            </td></tr>
<tr><td> windows      </td><td> amd64          </td></tr></tbody></table>

</blockquote>

<ul><li>$GOHOSTOS, $GOHOSTARCH
<blockquote>
호스트 운영체제와 컴파일 아키텍처 이름. 로컬 시스템의 운영체제와 아키텍처 이름이 기본값으로 설정된다.</li></ul>

사용가능한 목록은 위에서 언급한 $GOOS, $GOARCH와 같다. 설정한 값은 로컬 시스템과 반드시 호환 가능해야 한다.<br>
예를 들어, x86시스템에서 $GOHOSTARCH 값으로 arm 을 할당할 수는 없다.<br>
</blockquote>

<ul><li>$GOBIN
<blockquote>
Go 바이너리가 설치된 장소. 기본값은 $GOROOT/bin. 설치 후, 사용자의 $PATH에 $GOBIN에 사용된 디렉토리를 추가하면 go 도구들을 사용할 수 있다. $GOBIN이 설정되어 있으면 go 명령어는 $GOBIN 환경변수가 지정한 장소에 설치된다.<br>
</blockquote>

  * $GO386(386 환경일 때만 사용 가능, 기본 값은 사용자 환경 - 386, arm64, 387 등 -을 검사하여 자동 설정된다.)
<blockquote>
이 환경변수는 8g에 의해 코드가 생성될 때, 부동 소수점(floating point) 계산시 387 floating-point 유닛(387로 설정)과 SSE2 명령어(sse2로 설정) 중 어떤 것을 사용할 지 정한다.</li></ul>

GO386-387 : 부동 소수점 연산에 x87을 사용한다; 모든 x86 칩을 지원해야 한다.(펜티엄 MMX 이상)<br>
<br>
GO386=sse2 : 부동 소수점 연산에 SSE2를 사용한다; 387보다 나은 퍼포먼스를 내지만, 펜티엄 4/옵테론/에슬론64 이상만 가능하다.<br>
</blockquote>

<ul><li>$GOARM(arm 환경일 때만 사용 가능. 기본 값은 타켓 프로세서에 대해 빌드 될 때는 자동으로 설정되며, 그 이외에는 6으로 설정된다.)
<blockquote>
이 환경변수는 런타임시 타겟팅해야 하는 ARM 부동 소수점 co-processor 아키텍처 버전을 설정한다. 만약 특정 타겟 시스템에 대해 컴파일하면, 이 환경변수에 대한 값은 로컬 시스템을 검사하여 자동 설정된다.</li></ul>

GOARM=5 : 소프트웨어 부동 소수점을 사용한다; CPU가 VFP co-processor가 없을 때.<br>
<br>
GOARM=6 : VFPv1 일 경우에만 사용. 크로스 컴파일시 기본값. 일반적으로 ARM11 또는 그 이상의 cores(VFPv2 또는 그 이상도 지원함)<br>
<br>
GOARM=7 : VFPv3 사용.  일반적으로 Cortex-A cores.<br>
</blockquote>

잘 모를 경우 이 환경 변수는 설정하지 말고 남겨두어라. 만약, Go 를 처음 실행 할 때 $GOARM 환경 변수가 요구된다면 그때 다시 설정하면 된다. Go의 ARM 지원에 관한 자세한 정보 <a href='https://golang.org/wiki'>Go 커뮤니티 위키</a>의  <a href='https://golang.org/wiki/GoArm'>GoARM</a>페이지를 참조하라.<br>
<br>
$GOARCH와 $GOOS 는 사용자가 현재 사용 중인 로컬 시스템 환경이 아니라 타겟 환경을 의미한다.<br>
당신은 항상 크로스 컴파일을 사용하고 있다.  아키텍처를 기준으로 우리는 타겟 환경에서 실행할 수 있는 여러 종류의<br>
바이너리를 제공한다. 32비트 운영체제가 동작하고 있는 x86-64 시스템에서는 GOARCH를 amd64가 아닌 386으로 설정해야 한다.<br>
<br>
만약 기본값을 재설정하고 싶다면, shell profile($HOME/.bashrc, $HOME/.profile 등에 위치함)에 위에서 언급한 환경변수들을 설정하라. 설정은 아마도 다음과 같은 모습일 것이다.<br>
<br>
<pre><code>export GOROOT=$HOME/go
export GOARCH=amd64
export GOOS=linux
```

다시 한번 말하지만, Go 트리를 개발하고, 빌드하고, 설치하기 위해 이러한 환경 변수를 설정할 필요는 없다.