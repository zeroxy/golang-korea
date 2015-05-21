  * original: http://code.google.com/intl/ko-KR/appengine/docs/go/gettingstarted/handlingforms.html
  * translated by: Jooyung Han
  * parent: [App Engine 시작하기](GoogleAppEngineGoGettingStarted.md)


# 폼 처리하기 #

사용자들이 직접 인사말을 남길 수 있도록 하려면, 어떻게든 사용자들이 입력양식을 통해 전송한 데이터를 처리해야 합니다. Go의 `http` 패키지를 이용하면 데이터를 쉽게 처리할 수 있습니다.

## 웹 입력양식(form) 처리하기 ##

`myapp/hello/hello.go` 파일을 다음과 같이 수정합니다.

```
package hello

import (
  "fmt"
  "http"
  "template"
)

func init() {
  http.HandleFunc("/", root)
  http.HandleFunc("/sign", sign)
}

func root(w http.ResponseWriter, r *http.Request) {
  fmt.Fprint(w, guestbookForm)
}

const guestbookForm = `
<html>
  <body>
    <form action="/sign" method="post">
      <div><textarea name="content" rows="3" cols="60"></textarea></div>
      <div><input type="submit" value="Sign Guestbook"></div>
    </form>
  </body>
</html>
`

func sign(w http.ResponseWriter, r *http.Request) {
  err := signTemplate.Execute(w, r.FormValue("content"))
  if err != nil {
    http.Error(w, err.String(), http.StatusInternalServerError)
  }
}

var signTemplate = template.MustParse(signTemplateHTML, nil)

const signTemplateHTML = `
<html>
  <body>
    <p>You wrote:</p>
    <pre>{@|html}</pre>
  </body>
</html>
`
```

페이지를 새로고침하면 폼(입력양식)이 나옵니다. 메시지를 입력하고 전송해보세요.

이 버전에는 핸들러가 두 개 있습니다. 하나는 `/` 경로와 연결된 `root`입니다. 입력양식을 보여줍니다. 다른 하나는 `/sign` 경로에 연결된 `sign`입니다. 입력양식을 통해 전송된 데이터를 보여줍니다.

`sign` 함수는 `r.FormValue`를 통해 입력양식 데이터를 가져와서 `signTemplate.Execute` 에 넘겨줍니다. `signTemplate.Execute`에서는 템플릿을 `http.ResponseWriter`로 내보냅니다. 템플릿에서는 입력된 데이터를  `html` 필터를 이용하여 HTML 특수문자를 이스케이프합니다.

## 다음은... ##

이제 사용자로부터 데이터를 받아왔습니다. 데이터를 저장해뒀다가 나중에 보여주기 위한 방법이 필요합니다.

[데이터스토어 이용하기](GoogleAppEngineGoUsingDatastore.md)로 계속.