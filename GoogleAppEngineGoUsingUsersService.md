  * original: http://code.google.com/intl/ko-KR/appengine/docs/go/gettingstarted/usingusers.html
  * translated by: Jooyung Han
  * parent: [App Engine 시작하기](GoogleAppEngineGoGettingStarted.md)

# 사용자 서비스 이용하기 #

Google App Engine은 구글의 인프라를 바탕으로 한 유용한 서비스들을 제공합니다. 그 중 하나가 사용자 서비스입니다. 이를 이용하면 여러분의 애플리케이션을 구글의 사용자 계정과 연동할 수 있습니다. 여러분의 애플리케이션에 사용자들이 로그인하는데 구글 계정을 이용하도록 할 수 있습니다.

사용자 서비스를 이용하면 쉽게 애플리케이션이 전하는 인사말을 개인화할 수 있습니다.

## Users 이용하기 ##

`myapp/hello/hello.go` 파일을 아래와 같이 수정합니다.

```
package hello

import (
  "appengine"
  "appengine/user"
  "fmt"
  "http"
)

func init() {
  http.HandleFunc("/", handler)
}

func handler(w http.ResonseWriter, r *http.Request) {
  c := appengine.NewContext(r)
  u := user.Current(c)
  if u == nil {
    url, err := user.LoginURL(c, r.URL.String())
    if err != nil {
      http.Error(w, err.String(), http.StatusInternalServerError)
      return
    }
    w.Header().Set("Location", url)
    w.WriteHeader(http.StatusFound)
    return
  }
  fmt.Fprintf(w, "Hello, %v!", u)
}
```

웹브라우저에서 새로고침을 누르세요. 여러분의 애플리케이션은 링크를 하나 보여줍니다. 링크를 따라가면 구글 로그인 페이지의 로컬 버전으로 연결됩니다. 이것은 여러분의 애플리케이션을 테스트하기에 적당한 것으로서, 여기에 아무 사용자명을 입력해도 됩니다. 입력한 내용에 맞게 가짜의 `user.User` 값을 만들어줍니다.

여러분의 애플리케이션이 실제 App Engine에서 돌아갈때는 구글 계정 로그인 화면으로 연결되며 로그인을 성공하면 여러분의 애플리케이션 페이지로 돌아오게 됩니다.

## Users API ##

새로 추가한 내용을 하나씩 살펴보겠습니다.

```
c := appengine.NewContext(r)
```

`appengine.NewContext` 함수는 현재의 요청과 연관된 `appengine.Context` 값을 반환합니다. 이 값은 Go SDK의 많은 함수들이 App Engine 서비스를 이용할 때 사용됩니다.

```
u := user.Current(c)
```

사용자가 이미 로그인하였다면 해당 사용자의 `user.User` 값을 반환합니다. 그렇지 않다면 `nil`을 반환합니다.

```
if u == nil {
  url, err := user.LoginURL(c, r.URL.String())
  if err != nil {
    http.Error(w, err.String(), http.StatusInternalServerError)
    return 
  }
  w.Header().Set("Location", url)
  w.WriteHeader(http.StatusFound)
  return
}
```

사용자가 아직 로그인하지 않았다면 브라우저를 구글 계정의 로그인 페이지로 리다이렉션시킵니다. (`Location`을 지정하고 302 "Found" HTTP 상태값을 반환) 리다이렉션할 때 원래의 페이지 URL(`r.URL.String()`)을 포함하므로 구글 계정 로그인 절차가 끝나면 사용자는 원래의 페이지로 돌아오게 됩니다.

`user.LoginURL` 함수는 두번째 출력 인자로 `os.Error` 값을 반환합니다. 에러가 발생할 것 같지는 않지만 체크하여 사용자에게 보여주는 것은 좋은 습관입니다. 여기에서는 `http.Error` 를 이용하여 보여주게 됩니다.

```
fmt.Fprintf(w, "Hello, %v!", u)
```

사용자가 로그인했다면 해당 사용자 계정으로부터 이름을 얻어와서 개인화된 인사말 메시지를 보여줍니다. 여기서 `fmt.Fprintf` 함수는 `*user.User`의  `String`함수(메소드)를 호출하여 사용자 이름을 문자열 형태로 가져옵니다.

Users API를 더 알고 싶다면 [Users 문서](http://code.google.com/appengine/docs/go/users/)를 참조하세요.

**주의:** 로그인한 사용자에게만 보여줄 경로가 있다면 `app.yaml` 설정파일에 `login: required` 설정을 지정하면 됩니다. 더 자세한 내용은 [app.yaml 문서](http://code.google.com/appengine/docs/python/config/appconfig.html)를 참조하세요.

## 다음은... ##

우리가 만든 애플리케이션은 방문자의 이름을 보여주며 맞이합니다. 사용자들이 서로 인사를 주고 받을 수 있도록 기능을 추가해볼까요?

[폼 처리하기](GoogleAppEngineGoHandlingForms.md)로 계속