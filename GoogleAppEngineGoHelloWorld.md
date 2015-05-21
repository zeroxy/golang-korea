  * original: http://code.google.com/intl/ko-KR/appengine/docs/go/gettingstarted/helloworld.html
  * translated by: Jooyung Han
  * parent: [App Engine 시작하기](GoogleAppEngineGoGettingStarted.md)

# Hello, World! #

Go App Engine 애플리케이션은 Go의 [http package](http://golang.org/pkg/http/)를 이용하여 웹 서버와 통신하며, 이를 통해 바깥 세상과 통신하게 됩니다. 이러한 점에서 Go App Engine 애플리케이션은 독립적인 Go 웹 애플리케이션을 만드는 것과 매우 유사합니다.

짧은 메시지를 보여주는 작은 애플리케이션을 만드는 것을 시작해 보겠습니다.

## 간단한 HTTP 핸들러 만들기 ##

`myapp` 이라는 디렉토리를 만듭니다. 애플리케이션 관련한 모든 파일을 이 디렉토리에 만들것입니다.

`myapp` 디렉토리 안에 `hello`라는 하위 디렉토리를 만듭니다. 여기에는 `hello` 패키지를 구성할 Go 소스파일들을 저장할 것입니다.

`hello` 디렉토리 안에 `hello.go` 파일을 만들어 내용을 다음과 같이 만듭니다.

```
package hello

import (
  "fmt"
  "http"
)

func init() {
  http.HandleFunc("/", handler)
}

func handler(w http.ResponseWriter, r *http.Request) {
  fmt.Fprint(w, "Hello, world!")
}
```

이 Go 패키지는 모든 요청에 대해 `Hello, world!`라는 메시지를 응답으로 보냅니다.

**주의:** 독립형 Go 프로그램을 작성할 때는 `main` 패키지에 넣겠지만, Go App Engine  런타임이 특별한 형태의 `main` 패키지를 제공하기 때문에 여러분은 HTTP 핸들러를 포함하는 여러분만의 패키지(이 경우, `hello`)를 만들어야 합니다.

## 설정 파일 만들기 ##

App Engine 애플리케이션에는 `app.yaml`이라는 설정 파일이 있습니다. 이 설정 파일을 통해서 App Engine 서비스가 어떤 런타임을 사용할지, 그리고 어떤 URL을 Go 프로그램이 다루게 될지 알려주게 됩니다.

`myapp` 디렉토리 안에 `app.yaml` 파일을 다음 내용으로 만듭니다.

```
application: helloworld
version: 1
runtime: go
api_version: 2

handlers:
- url: /.*
  script: _go_app
```

위해서부터 살펴보겠습니다.
  * 애플리케이션 id는 `helloworld`입니다. 여러분이 튜토리얼을 마친 다음 직접 만든 애플리케이션을 App Engine에 등록하려면 유일한 id값을 지정해줘야 합니다. 개발 중에는 어떤 값이든 상관없습니다. 당장은 `helloworld`로 해 둡니다.
  * 애플리케이션 코드의 버전이 1입니다. 여러분이 애플리케이션을 수정하여 새로 업로드할때 이 값을 조정하게 됩니다. App Engine은 이전 버전을 유지해주어 관리자 콘솔을 이용하여 나중에 이전 버전으로 되돌리기 할 수 있습니다.
  * 이 코드는 `go` 런타임 환경에서 버전 2를 기준으로 동작합니다.
  * 정규표현식 `/.*` (모든 URL)에 일치하는 모든 URL 요청에 대해 Go 프로그램이 처리합니다. `_go_app` 이라는 값은 `dev_appserver.py`에 전달하기 위한 값이며, 나중에 실제 App Engine 서버에서 동작할 때는 무시됩니다.

**주의:** Go SDK는 Python이나 Java의 SDK와는 다른 점이 있습니다. 애플리케이션이 이용하는 Go의 패키지는 모두 단일 실핼파일로 포함됩니다. 요청에 대한 분기 처리도 Go 프로그램이 직접 처리합니다. `init`함수에서 `http.HandleFunc`를 호출하여 `handler`를 웹 루트(`"/"`)에 연결하는 것도 이 때문입니다. 하지만 여전히 `app.yaml` 설정파일을 이용하여 정적 파일이나 특별한 권한이 필요한 요청을 처리할 수 있습니다.

설정 파일은 [YAML](http://www.yaml.org/)포맷입니다. 설정가능한 모든 옵션을 확인하려면 [app.yaml 문서](http://code.google.com/appengine/docs/python/config/appconfig.html)를 참조하세요.

## 애플리케이션 테스트하기 ##

`hello` 패키지와 설정 파일을 통해 모든 URL요청을 Go 프로그램이 처리하도록 만들었으므로 애플리케이션이 완성되었습니다. 여러분은 SDK에 포함된 웹 서버를 이용하여 이를 테스트할 수 있습니다.

우리가 만든 파일들이 모두 제 위치에 있는지 확인하세요. 애플리케이션 디렉토리 구조는 다음과 같아야 합니다.

```
myapp/
  app.yaml
  hello/
  hello.go
```

다음 명령을 실행하여 애플리케이션을 컴파일하고 개발용 웹서버를 실행하세요.

```
/path/to/google_appengine/dev_appserver.py myapp/
```

(이미 `PATH` 환경변수를 설정해놓았다면 `dev_appserver.py`만 입력해도 됩니다.)

웹서버가 동작하면 8080포트로 들어오는 요청을 대기하게 됩니다. 다음 주소를 웹브라우저로 방문하여 애플리케이션의 동작을 테스트해보세요.

> http://localhost:8080/

개발용 웹서버의 실행과 관련하여 더 알아보고 싶다면(예를 들어 포트를 바꾼다든지) [개발용 웹서버 문서](http://code.google.com/appengine/docs/python/tools/devserver.html)를 참조하거나, 명령어 옵션 `--help`으로 실행해보세요.

## 반복적 개발 ##

개발용 서버는 여러분이 작성한 파일들 중 어떤 것이 수정되었는지 체크합니다. 소스코드 중 일부를 수정하면 다시 컴파일하여 애플리케이션을 다시 실행해줍니다. 여러분이 직접 `dev_appserver.py`를 재시작할 필요가 없습니다.

확인해볼까요? 웹서버가 실행중인 상태로 두고, `hello.go`파일을 수정하여 `Hello, world!` 메시지를 다른 내용을 바꾸어 저장해보세요. 웹브라우저의 다시불러오기를 통해 http://localhost:8080/ 에서 바뀐 내용을 확인해보세요.

웹서버를 종료하려면 터미널창에서 컨트롤-C(혹은 종료에 해당하는 키)를 누르세요.

이 튜토리얼을 진행하는 동안에는 웹서버를 계속 띄워 놓아도 됩니다. 혹시 종료해야 한다면 위에서와 같이 명령어를 입력하여 재시작하면 됩니다.

## 다음은... ##

App Engine 애플리케이션을 하나 완성했습니다! 간단하지만 인사말을 건네주는 이 애플리케이션을 배포하여 전세계의 사용자들과 공유할 수 있습니다. 하지만 배포하기에 앞서 몇가지 기능을 더 추가해봅시다.

[사용자 서비스 이용하기](GoogleAppEngineGoUsingUsersService.md)로 계속