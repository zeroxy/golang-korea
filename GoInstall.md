# 요약 #
  * 내용 : Go 시작하기
  * 원문 : [Getting Started](http://golang.org/doc/install)
  * 번역자 : icebluelove7@gmail.com




# Go 배포판 다운로드 #

> [다운로드](https://golang.org/dl/)

> [공식 바이너리 배포판](https://golang.org/dl/)은 FreeBSD(릴리즈 8 이상), 리눅스, 맥 OS X(스노우 레오파드 이상), 윈도우 운영체제, 32비트(386), 64비트(amd64) x86 프로세서 아키텍처에서 이용 가능하다. 만약 당신의 시스템이 위에서 언급한 지원대상에 포함되어 있지 않아 바이너리 배포판 설치가 불가능할 경우, [go 소스파일을 직접 설치](http://golang.org/doc/install/source)하거나, [gc 대신 gccgo를 설치](http://golang.org/doc/install/gccgo)할 수 있다.


# 시스템 요구사항 #
gc 컴파일러는 아래의 운영체제와 아키텍처를 지원한다. 설치를 진행하기 전에 당신의 시스템이 아래의 조건을 충족하는지 반드시 확인하기 바란다. 만약 당신의 운영체제나 아키텍처가 아래의 목록에 없다면 gccgo를 통한 설치를 시도해 볼 수 있다. : [gcc 를 이용한 설치와 gcc 사용법 보기](http://golang.org/doc/install/gccgo)

| **운영체제** | **아키텍처** | **참고사항** |
|:-----------------|:-----------------|:-----------------|
| FreeBSD 8 이상 | amd64, 386, arm  | 데비안 GNU/kFreeBSD 는 지원하지 않음; FreeBSD/ARM 은 FreeBSD 10 이상 |
| 리눅스 2.6.23 이상(glibc 사용) | amd64, 386, arm  | CentOS/RHEL 5.x 은 지원하지 않음; 현재 ARM에 대한 바이너리 배포판은 없음. |
| 맥 OS X 10.6 이상 | amd64, 386       | Xcode에서는 gcc를 사용하라. |
| 윈도우 XP 이상 | amd64, 386       | MinGW gcc를 사용하라. cygwin 또는 msys 는 필요없음. |

  * cgo를 사용할 예정이라면 gcc가 필요하다.
  * gcc 사용을 위해서는 Xcode command line tools 설치가 필요하다. 만약 Xcode 4.3 버전 이상이 설치되어 있다면, Xcode-Preference-Downloads panel-components 탭에서 command line tools를 설치할 수 있다.


# Go 도구 설치 #
만약 Go를 업그레이드하는 경우라면, 먼저 현재 설치된 버전을 삭제해야 한다.

## 리눅스, 맥 OS X, FreeBSD tar 압축파일 ##
[저장소에서 파일을 다운로드](https://golang.org/dl/) 받은 다음, _/usr/local_ 디렉토리에 압축을 풀고, _/usr/local/go_ 디렉토리에 Go 하위 디렉토리를 생성한다. 예시 :
```
tar -C /usr/local -xzf go$VERSION.$OS-$ARCH.tar.gz
```

설치를 위해 적절한 파일을 선택하라. 예를 들어, 리눅스 64비트 x86 시스템에 Go 1.2 버전을 설치하고자 한다면, _go1.2.1.linux-amd64.tar.gz_ 파일을 선택해야 한다.

(일반적으로 이러한 명령어들은 root 또는 sudo 권한으로 실행해야 한다.)

PATH 환경변수에 _/usr/local/go/bin_ 을 추가하기 위해, _/etc/profile_ (시스템 전체 설치일 경우) 또는 _$HOME/.profile_ 파일을 열어 아래의 구문을 삽입하자.
```
export PATH=$PATH:/usr/local/go/bin
```

### 설치 디렉토리 변경 ###
Go 바이너리 배포판은 기본적으로 _/usr/local/go_ 디렉토리(윈도우 운영체제는 _c:\Go_ 디렉토리)에 설치되지만, Go tools를 다른 디렉토리에 설치할 수 있다. 특정 디렉토리에 설치하기 위해서는 우선, 설치 디렉토리를 나타내는 GOROOT 환경변수가 설정되어 있어야 한다.

예를 들어, Go를 사용자의 home 디렉토리에 설치하고자 한다면 _$HOME/.profile_ 파일에 아래의 구문을 추가해야 한다.
```
export GOROOT=$HOME/go export PATH=$PATH:$GOROOT/bin
```

**주의** : GOROOT 는 기본 디렉토리가 아닌 사용자가 지정한 특정 디렉토리에 Go를 설치할 경우에만 설정하라.

## 맥 OS X 패키지 인스톨러 ##
[패키지 파일을 다운로드](https://golang.org/dl/) 한 다음, 파일을 열고, Go tools 설치 안내창을 따라 설치를 진행한다. 패키지는 _/usr/local/go_ 디렉토리에 Go 배포판을 설치한다.
사용자 PATH 환경 변수에 _/usr/local/go/bin_ 디렉토리를 추가하라. 환경변수 변경 사항을 반영하기 위해서는 터미널 프로그램 재실행이 필요할 수도 있다.

## 윈도우 운영체제 ##
Go 프로젝트는 윈도우 운영체제 사용자를 위해 두 가지 설치 방법을 제공한다.([소스파일에서 설치하는 방법](http://golang.org/doc/install/source)도 있음) : zip 압축파일을 이용한 설치(몆 가지 환경 변수 설정이 필요), MSI 인스톨러(자동 설정)

### MSI 인스톨러 ###
[MSI 파일](https://golang.org/dl/)을 열고, Go tools 설치 안내창을 따라 설치를 진행한다. 기본적으로, 인스톨러는 _c:\Go_ 디렉토리에 Go 배포판을 설치한다. 사용자 PATH 환경변수에 _c:\Go\bin_ 디렉토리를 추가하라.

### Zip 압축파일 ###
[Zip 파일을 다운로드](https://golang.org/dl/) 한 뒤, 특정 디렉토리에 압축을 푼다(_c:\Go_ 디렉토리를 추천한다.)
만약 _c:\Go_ 이외의 디렉토리를 선택했다면, 해당 디렉토리를 GOROOT 환경 변수 값으로 설정해야 한다.
사용자 PATH 환경변수에 Go 루트 디렉토리의 서브디렉토리인 bin (예, _c:\Go\bin_)을 추가하라.

### 윈도우에서 환경 변수 설정하기 ###
윈도우 운영체제에서는 시스템 제어 패널의 Advanced 탭-환경 변수(Environment Variables) 버튼을 통해 환경 변수를 설정할 수 있다. 특정 버전의 윈도우 운영체제에서는 시스템 제어 패널-Advanced System  Settings 옵션에서 설정할 수 있다.


# 설치 완료 확인 #
Go 가 제대로 설치되었는지 확인하기 위해 간단한 프로그램 하나를 빌드해보자.
hello.go 라는 파일을 만들고 아래의 코드를 추가한다.

```
package main

import "fmt"

func main() {
     fmt.Printf("hello, world\n") 
}
```

그리고, go 도구를 이용해 프로그램을 실행한다.

```
$ go run hello.go
hello, world
```

만약 hello. world 메시지가 보인다면 Go를 제대로 설치한 것이다.


# 작업 환경 설정 #
이제 거의 완료되었다. 작업 환경 설정을 해보자.
[Go Code 작성하기](https://code.google.com/p/golang-korea/wiki/HowToWriteGoCode)를 참조하여 Go tools를 사용하기 위해 필요한 명령어들을 익혀보자.


# Go 삭제 #
시스템에 설치된 Go를 삭제하려면 go 디렉토리를 제거하면 된다. 이 디렉토리는 일반적으로 리눅스, 맥 OS X, FreeBSD에서는 _/usr/local/go_, 윈도우 운영체제에서는 _c:\Go_ 에 위치한다.

또한, 사용자 PATH 환경변수에서 Go bin 디렉토리를 삭제해야 한다. 리눅스, FreeBSD에서는 _/etc/profile_ 또는 _$HOME/.profile_ 을 편집하면 된다. 만약 맥 OS X 패키지로 Go를 설치한 경우,
_/etc/paths.d/go_ 파일을 삭제해야 한다. 윈도우 운영체제 사용자들은 [윈도우에서 환경 변수 설정하기 섹션](https://code.google.com/p/golang-korea/wiki/GoInstall#윈도우에서_환경_변수_설정하기)을 참조하라.


# 도움받기 #
  * [Freenode IRC 서버](http://freenode.net/)의 go-nuts 채널을 통해 고퍼들로부터 실시간 도움을 받을 수 있다.
  * Go 언어 관련 토론을 위한 공식 메일링 리스트는 [Go Nuts](http://groups.google.com/group/golang-nuts) 이다.
  * 버그 신고는 [Go issue tracker](http://golang.org/issue)를 이용하라.