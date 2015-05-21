  * original page: http://code.google.com/intl/ko-KR/appengine/docs/go/gettingstarted/usingdatastore.html
  * translated by: Jooyung Han
  * parent: [App Engine 시작하기](GoogleAppEngineGoGettingStarted.md)

# 데이터스토어 이용하기 #

확장성 있는 웹 애플리케이션에서 데이터를 저장하는 것은 꽤 복잡합니다. 사용자는 동시에 여러개의 웹 서버와 상호작용할 수도 있고, 사용자의 다음 요청이 이전 요청을 처리한 서버와는 다른 서버로 전달되기도 합니다. 웹 서버 각각은 여러 컴퓨터에 흩어진 데이터에 의존하고 있을 수도 있습니다. 실제 세계 각지에 흩어진 컴퓨터일 수도 있습니다.

Google App Engine 덕분에 여러분은 이런 내용을 전혀 걱정할 필요가 없습니다. App Engine의 인프라는 간단한 API 뒤에 숨어있을 데이터의 분산, 중복, 로드밸런싱 등의 복잡한 내용을 모두 처리해줍니다. 그리고 강력한 질의(query) 엔진도 제공합니다.

## 데이터스토어를 이용하는 완성된 예제 ##

`myapp/hello/hello.go` 의 새로운 버전은 다음과 같습니다. 사용자가 남기는 인사말을 데이터스토어에 저장합니다. 새로 추가된 내용을 하나씩 살펴보겠습니다.

```
package hello

import (
  "appengine"
  "appengine/datastore"
  "appengine/user"
  "http"
  "template"
  "time"
)

type Greeting struct {
  Author  string
  Content string
  Date    datastore.Time
}

func init() {
  http.HandleFunc("/", root)
  http.HandleFunc("/sign", sign)
}

func root(w http.ResponseWriter, r *http.Request) {
  c := appengine.NewContext(r)
  q := datastore.NewQuery("Greeting").Order("-Date").Limit(10)
  greetings := make([]Greeting, 0, 10)
  if _, err q.GetAll(c, &greetings); err != nil {
    http.Error(w, err.String(), http.StatusInternalServerError)
    return
  }
  if err := guestbookTemplate.Execute(w, greetings); err != nil {
    http.Error(w, err.String(), http.StatusInternalServerError)
  }
}

var guestbookTemplate = template.MustParse(guestbookTemplateHTML, nil)

const guestbookTemplateHTML = `
<html>
  <body>
    {.repeated section @}
      {.section Author}
        <p><b>{@|html}</b> wrote:</p>
      {.or}
        <p>An anonymous person wrote:</p>
      {.end}
      <pre>{Content|html}</pre>
    {.end}
    <form action="/sign" method="post">
      <div><textarea name="content" rows="3" cols="60"></textarea>
      <div><input type="submit" value="Sign Guestbook"></div>
    </form>
  </body>
</html>
`

func sign(w http.ResponseWriter, r *http.Request) {
  c := appengine.NewContext(r)
  g := Greeting{
    Content: r.FormValue("content"),
    Date:    datastore.SecondsToTime(time.Seconds()),
  }
  if u := user.Current(c); u != nil {
    g.Author = u.String()
  }
  _, err := datastore.Put(c, datastore.NewIncompleteKey("Greeting"), &g)
  if err != nil {
    http.Error(w, err.String(), http.StatusInternalServerError)
    return
  }
  http.Redirect(w, r, "/", http.StatusFound)
}
```

`myapp/hello/hello.go` 파일을 위처럼 바꾼 다음 http://localhost:8080/ 페이지를 새로고침 합니다. 메시지를 남기고 그것이 정상적으로 저장되었다가 화면에 나타나는지 확인해보세요.

## 전송된 인사말 저장하기 ##

방명록 애플리케이션에서 우리는 사용자가 전송한 인사말을 저장하려고 합니다. 각각의 인사말에는 그것을 작성한 사람의 이름과 메시지 내용, 그리고 메시지가 작성된 날짜와 시간 정보가 포함됩니다. 보여줄 때는 시간 순으로 보여줄 것입니다.

이러한 데이터를 나타내기 위해 Go 구조체(struct) `Greeting`을 만들었습니다.

```
type Greeting struct {
  Author  string
  Content string
  Date    datastore.Time
}
```

인사말을 다루기 위한 자료형이 만들어졌습니다. 애플리케이션은 새로운 `Greeting` 객체를 만들 수 있고, 이를 데이터스토어에 저장할 수 있게 됐습니다. `sign` 핸들러에서 바뀐 부분을 볼까요?

```
func sign(w http.ResponseWriter, r *http.Request) {
  c := appengine.NewContext(r)
  g := Greeting{
    Content: r.FormValue("content"),
    Date:    datastore.SecondsToTime(time.Seconds()),
  }
  if u := user.Current(c); u != nil {
    g.Author = u.String()
  }
  _, err := datastore.Put(c, datastore.NewIncompleteKey("Greeting"), &g)
  if err != nil {
    http.Error(w, err.String(), http.StatusInternalServerError)
    return
  }
  http.Redirect(w, r, "/", http.StatusFound)
}
```

`Greeting` 값을 새로 만들고, `Author` 필드를 현재 사용자로 설정합니다. `Content` 필드는 사용자가 전송한 값으로 설정합니다. `Date` 필드는 현재 시간으로 설정합니다.

마지막으로 `datastore.Put` 을 호출하여 새 값을 데이터스토어에 저장합니다. 불완전 키를 새로 만들어 전달하면 데이터스토어가 자동으로 해당 레코드에 필요한 키를 생성하게 됩니다.

## 저장된 인사말을 datastore.Query로 읽어오기 ##

`datastore` 패키지는 `Query` 타입을 제공하여 데이터스토어에서 질의하는 것과 결과를 순차적으로 읽어오는 것을 지원합니다.

`root` 핸들러는 데이터스토어에서 인사말을 질의하는 것을 보여줍니다.

```
func root(w http.ResponseWriter, r *http.Request) {
  c := appengine.NewContext(r)
  q := datastore.NewQuery("Greeting").Order("-Date").Limit(10)
  greetings := make([]Greeting, 0, 10)
  if _, err q.GetAll(c, &greetings); err != nil {
    http.Error(w, err.String(), http.StatusInternalServerError)
    return
  }
  if err := guestbookTemplate.Execute(w, greetings); err != nil {
    http.Error(w, err.String(), http.StatusInternalServerError)
  }
}
```

우선은 `Greeting` 객체를 `Date` 내림차순으로 요청하는 `Query` 객체를 만듭니다. 이때 최대로 가져오는 값을 10개로 한정하고 있습니다.

```
q := datastore.NewQuery("Greeting").Order("-Date").Limit(10)
```

그런다음 `q.GetAll(c, &greetings)`를 통해 질의를 실행하고 질의 결과를 `greetings` 슬라이스에 저장합니다.

```
greetings := make([]Greeting, 0, 10)
if _, err := q.GetAll(c, &greetings); err != nil {
    http.Error(w, err.String(), http.StatusInternalServerError)
    return
}
```

마지막으로 `guestbookTemplate.Execute` 함수를 호출하여 HTML 페이지를 보여줍니다. 데이터스토어에서 읽어들인 인사말을 포함하여 HTML을 렌더링하고, 이를 `http.ResponseWriter` 이용하여 출력합니다. 템플릿 언어를 더 알고 싶다면 [`template` 패키지 문서](http://golang.org/pkg/template/)를 참고하세요.

데이터스토어 API에 대한 완전한 설명을 보려면 [데이터스토어 문서](http://code.google.com/appengine/docs/go/datastore/)를 참고하세요.

## 개발용 서버의 데이터스토어를 모두 지우기 ##

개발용 웹서버는 로컬 버전의 데이터스토어를 이용하여 여러분의 애플리케이션을 테스트할 수 있도록 합니다. 데이터는 임시 파일에 저장되기 때문에 임시 파일이 삭제되지 않는한 데이터가 계속 유지되며, 웹서버는 여러분이 따로 요청하지 않는한 임시 파일을 삭제하지 않습니다.

애플리케이션을 시작하기에 앞서 개발용 서버의 데이터스토어가 저장한 모든 데이터를 지우고자 한다면 `--clear_datastore` 옵션을 주어 서버를 시작하면 됩니다.

```
dev_appserver.py --clear_datastore myapp/
```

## 다음은... ##

우리가 완성한 방명록 애플리케이션은 완전하게 동작합니다. 구글 계정을 이용하여 사용자를 인증하며, 메시지를 전송할 수 있고, 전송된 메시지와 다른 사용자들이 남긴 메시지를 보여주기도 합니다. App Engine이 확장성 문제를 자동으로 처리해주기 때문에 이 애플리케이션이 인기를 얻게 되었다고 해서 구조를 고칠 필요가 없습니다.

[애플리케이션 업로드하기](GoogleAppEngineGoUploadingYourApplication.md)에서 계속